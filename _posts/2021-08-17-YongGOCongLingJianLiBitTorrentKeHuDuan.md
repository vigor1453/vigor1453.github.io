---
layout: post
title: "用GO从零建立BitTorrent客户端"
date: 2021-08-17
tags: ["go"]
comments: true
author: oneman233
excerpt: "一篇翻译"
toc: true
---

[原文地址](https://blog.jse.li/posts/torrent/)

---

**tl;dr:** 在本文中，我们将会实现足够的 BitTorrent 协议来下载 Debian。源码在[这里](https://github.com/veggiedefender/torrent-client/)，或者你也可以直接跳到[最后一段](#全部组合到一块儿)。

BitTorrent 是一个在互联网上下载和分享文件的协议。传统的 client/server 关系中，下载者们都连接到一个中央服务器（例如：在 Netflix 上看电影，或者加载你正在阅读的网页），而在 BitTorrent 网络中，下载者被称为 **peers**，他们互相之间下载文件的某一个片段——这就是被称为 **peer-to-peer（P2P）** 的协议。我们将研究它是如何工作的，并且构建我们自己的能够找到 peers 以及与其他 peers 交换数据的服务器。

这个协议在过去 20 年间不断进化，许多个人和组织给它添加了许多新的特性，例如：加密、私人种子和新的找到 peers 的方法。我们只实现它在 2001 年发布的[最初版本](https://www.bittorrent.org/beps/bep_0003.html)，来保证这是一个能在一个周末内完成的项目。

我会使用 [Debian 的镜像文件](https://cdimage.debian.org/debian-cd/current/amd64/bt-cd/#indexlist)来作为测试对象，它 350MB 的大小刚好合适。作为一个受欢迎的 Linux 版本，我们能找到很多高速的和合作的 peers 来连接，同时我们还能规避法律上的和道德上的关于下载盗版资源的问题。

# 寻找 Peers

这里有一个问题：我们希望用 BitTorrent 下载一个文件，但是它是一个 P2P 协议，所以我们不知道上哪里去找到 peers 来下载文件。这就像刚搬到一个新的城市并且尝试着交朋友——也许我们会去当地的酒吧或者聚会！像这样的集中地点是 **trackers** 背后的根本原理，trackers 是把 peers 介绍给其他 peers 的中央服务器。本质上它们只是运行在 HTTP 上的网络服务器，你可以在[这里](http://bttracker.debian.org:6969/)找到 Debian 的 tracker。

当然，如果这些中央服务器帮助 peers 来交换非法内容的话，FBI 很容易就会找上门。你也许记得 TorrentSpy、Popcorn Time 和 KickassTorrents 等等 tracker 被调查然后关门的消息。目前新的方法已经可以不依赖于中间人的参与，甚至把 **peer 发现** 都设计成了一个分布式的过程。我们并不会实现这些新的方法，但是如果你感兴趣的话，你可以搜索这些关键词：**DHT**、**PEX** 和 **magnet links**。

## 转换 .torrent 文件

.torrent 文件描述了可下载文件的内容和连接到特定 tracker 的信息，而这就是开始下载一个 torrent 所需要的全部。Debian 的 .torrent 文件长这样：

```
d8:announce41:http://bttracker.debian.org:6969/announce7:comment35:"Debian CD from cdimage.debian.org"13:creation datei1573903810e9:httpseedsl145:https://cdimage.debian.org/cdimage/release/10.2.0//srv/cdbuilder.debian.org/dst/deb-cd/weekly-builds/amd64/iso-cd/debian-10.2.0-amd64-netinst.iso145:https://cdimage.debian.org/cdimage/archive/10.2.0//srv/cdbuilder.debian.org/dst/deb-cd/weekly-builds/amd64/iso-cd/debian-10.2.0-amd64-netinst.isoe4:infod6:lengthi351272960e4:name31:debian-10.2.0-amd64-netinst.iso12:piece lengthi262144e6:pieces26800:�����PS�^�� (binary blob of the hashes of each piece)ee
```

这一堆乱码被用一种称为 **Bencode**（发音为 *bee-encode*）的方法进行了编码，我们需要想办法解码它。

Bencode 编码与 JSON 的结构大致相同——strings、integers、lists 和 dictionaries。Bencode 的数据不像 JSON 那样是人类可读的，但是它可以高效地处理二进制数据并且很容易地从流转换而来。Strings 的前缀代表了它们的长度，例如：`4:spam`。Integers 被夹在*开始*和*结束*符号之间，例如 `7` 会被编码成 `i7e`。Lists 和 dictionaries 与 integers 类似，例如：`l4:spami7e` 表示 `['spam',7]`，`d4:spami7ee` 表示 `{spam:7}`。

用一个更好看的形式，我们的 .torrent 文件长这样：

```
d
  8:announce
    41:http://bttracker.debian.org:6969/announce
  7:comment
    35:"Debian CD from cdimage.debian.org"
  13:creation date
    i1573903810e
  4:info
    d
      6:length
        i351272960e
      4:name
        31:debian-10.2.0-amd64-netinst.iso
      12:piece length
        i262144e
      6:pieces
        26800:�����PS�^�� (binary blob of the hashes of each piece)
    e
e
```

在这个文件中，我们能看到 tracker 的 URL、种子建立的时间（使用 Unix 时间戳表示）、文件的名称和大小和一个巨大的含有文件的各个 **piece** 的 SHA-1 哈希值的 BLOB（binary large object）。这个 BLOB 的大小与我们希望下载的文件的一部分是相等的。一个 piece 的实际大小根据不同的 torrents 会有一些变化，通常介于 256KB 和 1MB 之间。这意味着一个大文件可能由*数千个* pieces 组成。我们将要从其他 peers 那里下载这些 pieces，将他们与 .torrent 文件中的哈希值进行核对，把它们组装到一起，最后我们就得到了一个完整的文件！

这一机制允许我们在下载过程中验证每一个 piece 的完整性。这让 BitTorrent 具有了抵抗文件意外损坏和大规模 **torrent poisoning** 的能力。我们一定能得到我们希望下载的文件，除非某个攻击者能够使用原像攻击（哈希不可逆以及抗碰撞性——译者注）破解 SHA-1。

写一个 Bencode 解析器是很有意思的，但是这不是我们所关注的重点。但是我发现 Fredrik Lundh 写的 [50 line parser](https://web.archive.org/web/20200105114449/https://effbot.org/zone/bencode.htm) 非常有意思。在今天的项目中，我将会使用[这个解析器](https://github.com/jackpal/bencode-go)。

```go
type bencodeInfo struct {
	Pieces       string `bencode:"pieces"`
	PiecesLength int    `bencode:"piece length"`
	Length       int    `bencode:"Length"`
	Name         string `bencode:"name"`
}

type bencodeTorrent struct {
	Announce string      `bencode:"announce"`
	Info     bencodeInfo `bencode:"info"`
}

func Open(r io.Reader) (*bencodeTorrent, error) {
	bto := &bencodeTorrent{}
	err := bencode.Unmarshal(r, bto)
	if err != nil {
		return nil, err
	}
	return bto, nil
}
```

由于我希望保证我的结构尽量平坦，并且我喜欢把应用结构和序列化结构分离开来，我导出了一个不同的、更加平坦的称为 `TorrentFile` 的结构，然后我编写了一些帮助函数来在二者之间进行转换。

值得注意的是，我把 `pieces`（之前是 string 类型）转换成了一个哈希值的切片（每个都是 `[20]byte`），这样之后我就可以方便地访问到个人哈希值。我也计算了整个 Bencode 编码后的 `info` 字典（包含了name、size 和 piece hashes）的 SHA-1 哈希值。我们把它称为 **infohash**，它在我们与 trackers 和 peers 通信时唯一表示了我们希望下载的文件，稍后我们会进行详细介绍。

```go
type TorrentFile struct {
	Announce    string
	InfoHash    [20]byte
	PieceHashes [][20]byte
	PieceLength int
	Length      int
	Name        string
}

func (bto bencodeTorrent) toTorrentFile() (TorrentFile, error) {
	// ...
}
```

## 从 tracker 中获取 peers

现在我们有了关于文件和它的 tracker 的信息，让我们与 tracker 通信来将我们自己 **announce**（声明）为一个 peer 并获取其他 peers 的一个名单。我们只需要给 .torrent 文件中 `announce` 字段声明的 URL 发送一个 GET 请求即可，但是需要加上下面这些参数：

```go
func (t *TorrentFile) buildTrackerURL(peerID [20]byte, port uint16) (string, error) {
    base, err := url.Parse(t.Announce)
    if err != nil {
        return "", err
    }
    params := url.Values{
        "info_hash":  []string{string(t.InfoHash[:])},
        "peer_id":    []string{string(peerID[:])},
        "port":       []string{strconv.Itoa(int(Port))},
        "uploaded":   []string{"0"},
        "downloaded": []string{"0"},
        "compact":    []string{"1"},
        "left":       []string{strconv.Itoa(t.Length)},
    }
    base.RawQuery = params.Encode()
    return base.String(), nil
}
```

一些重要的参数：

* **info_hash**：唯一标识了我们试图下载的文件。这是我们之前从 Bencode 的 `info` 字典中计算而来的。tracker 会根据它来帮我们寻找对应的 peer。
* **peer_id**：一个 20byte 的名字，向 tracker 和其他 peer 表示了我们自己。我们只需要生成随机的 20byte 即可。实际的 BitTorrent 客户端的 ID 形如：`-TR2940-k8hj0wgej6ch`，这中 ID 标记了客户端的软件和版本（例如 TR2940 代表了传输客户端 2.94）

## 转换 tracker 的应答

我们得到了一个 Bencode 编码的应答：

```go
d
  8:interval
    i900e
  5:peers
    252:(another long binary blob)
e
```

`Interval` 告诉我们应该隔多久再次连接到 tracker 以刷新我们的 peers 列表。900 意味着我们应该每隔 15 分钟就重连一次（900 秒）。

`Peers` 是另一个 BLOB，它含有每个 peer 的 IP 地址。它由 **6byte 的分组**组成，每个分组中开头的 4byte 表示了 peer 的 IP 地址——每个 byte 代表了 IP 地址中的一个数字。最后的两个 byte 表示端口号，是一个大端法表示的 `uint16`。**大端法**，又被称为**网络序**，代表着我们可以从左到右地逐字节拆分一个分组。例如，字节 `0x1A` 和 字节 `0xE1` 组成了字节 `0x1AE1` ，用十进制表示就是 6881。

```go
// Peer 结构体对单个 peer 的连接信息进行编码
type Peer struct {
    IP   net.IP
    Port uint16
}

// Unmarshal 函数从缓存中读取 peer 的 IP 地址和端口号
func Unmarshal(peersBin []byte) ([]Peer, error) {
    const peerSize = 6 // IP 地址占 4 位，端口号占 2 位
    numPeers := len(peersBin) / peerSize // 计算获得了多少个 peers
    if len(peersBin)%peerSize != 0 {
        err := fmt.Errorf("Received malformed peers")
        return nil, err
    }
    peers := make([]Peer, numPeers)
    for i := 0; i < numPeers; i++ {
        offset := i * peerSize
        peers[i].IP = net.IP(peersBin[offset : offset+4])
        peers[i].Port = binary.BigEndian.Uint16(peersBin[offset+4 : offset+6])
    }
    return peers, nil
}
```

# 从 peers 那里下载资源

现在既然我们有了一个 peers 的名单，是时候与他们连接并且开始下载文件片段了！我们可以把这个过程分解成几个步骤，对每个 peer，我们希望：

1. 与 peer 建立TCP连接。就像拨个电话过去一样。
2. 完成一个双向的 BitTorrent 握手。“你好吗？”“你好。”
3. 交换**用于下载文件分段的信息**。“我需要分段 #231，谢谢。”

## 开启一个 TCP 连接

```go
conn, err := net.DialTimeout("tcp", peer.String(), 3*time.Second)
if err != nil {
    return nil, err
}
```

我设置了 3s 的过期时间，这样我就不会在那些不允许我连接的 peers 上浪费太多时间。大多数情况下，这都是一个非常标准的 TCP 连接。

## 完成握手

我们刚刚建立了到其他 peer 的一个链接，现在我们希望进行一次握手来验证我们的一些假设：

* 这个 peer 能使用 BitTorrent 协议
* 这个 peer 能理解并回复我们的消息
* 这个 peer 有我们需要的文件，或者至少知道我们在讨论哪个文件

我的父亲告诉我一次成功的握手的秘诀是紧紧握住和眼神交流。而一次成功的 BitTorrent 握手由五个部分组成：

1. 协议标识符的长度，它总是 19（用十六进制表示是 0x13）
2. 协议标识符，称为 `pstr`，它总是 `BitTorrent protocol`
3. 8 个**保留字节**，全部设置为 0。我们可以将它们当中的某些设置为 1 来表示我们支持特定的[扩展](http://www.bittorrent.org/beps/bep_0010.html)。但是我们不需要，所以我们只把它们设置为 0 就行
4. 我们之前计算过的用于标识我们希望下载的文件的 **infohash**
5. 用于表示我们自己的 **Peer ID**

加在一块儿，一个握手字符串长得像这样：

```
\x13BitTorrent protocol\x00\x00\x00\x00\x00\x00\x00\x00\x86\xd4\xc8\x00\x24\xa4\x69\xbe\x4c\x50\xbc\x5a\x10\x2c\xf7\x17\x80\x31\x00\x74-TR2940-k8hj0wgej6ch
```

在我们向其他 peer 发送了握手之后，我们应该接收一个格式相同的握手作为回复。回复中的 infohash 应当与我们发送的一致，只有这样我们才知道我们讨论的是同一个文件。如果一切就绪，我们就可以准备传输文件；如果出了些问题，我们则可以断开连接。

在我们的代码中，我们创建了一个用来表示握手的结构体，并且设计了几个方法用来对它们进行序列化和读取：

```go
// Handshake 是一种特别的 peer 用于声明自己的消息
type Handshake struct {
    Pstr     string
    InfoHash [20]byte
    PeerID   [20]byte
}

// Serialize 函数将 handshake 序列化到缓存中
func (h *Handshake) Serialize() []byte {
    buf := make([]byte, len(h.Pstr)+49)
    buf[0] = byte(len(h.Pstr))
    curr := 1
    curr += copy(buf[curr:], h.Pstr)
    curr += copy(buf[curr:], make([]byte, 8)) // 8 个保留字节
    curr += copy(buf[curr:], h.InfoHash[:])
    curr += copy(buf[curr:], h.PeerID[:])
    return buf
}

// 从流中读取并转换 handshake
func Read(r io.Reader) (*Handshake, error) {
    // 执行反序列化操作
    // ...
}
```

## 发送和接收消息

当我们完成了最初的握手后，我们就可以发送和接收**消息**。实际上现在我们还不能开始发送消息，直到另一个 peer 准备好接收消息。在这个状态中，我们被另一个 peer **choked**了。之后他会给我们发送 **unchoke** 消息来让我们知道我们可以开始向他请求消息了。在默认情况下，我们一直处于 choked 状态，除非另一个 peer 已经向我们发送了证明。

### 解析消息

一条消息的最开头是表示消息含有多少字节的长度标识符，它是一个由 4 个字节组成的 32bit 的整数，用大端法表示。接下来的一个字节被称为 ID，告诉我们我们收到的消息的类型，例如：`0x2` 字节表示 `interested`。最后，可选的**载荷**填充了消息的剩余部分。

```go
type messageID uint8

// 消息的类型常量
const (
    MsgChoke         messageID = 0
    MsgUnchoke       messageID = 1
    MsgInterested    messageID = 2
    MsgNotInterested messageID = 3
    MsgHave          messageID = 4
    MsgBitfield      messageID = 5
    MsgRequest       messageID = 6
    MsgPiece         messageID = 7
    MsgCancel        messageID = 8
)

// Message 结构体储存了一条消息的 ID 和载荷
type Message struct {
    ID      messageID
    Payload []byte
}

// Serialize 函数按照下面的格式把一条消息序列化到缓存中
// <length prefix><message ID><payload>
// 此外，空消息的含义为：keep-alive
func (m *Message) Serialize() []byte {
    if m == nil {
        return make([]byte, 4)
    }
    length := uint32(len(m.Payload) + 1) // +1 是给 ID 预留一个字节
    buf := make([]byte, 4+length)
    binary.BigEndian.PutUint32(buf[0:4], length)
    buf[4] = byte(m.ID)
    copy(buf[5:], m.Payload)
    return buf
}
```

为了从流中读取消息，我们只需要按照消息的格式进行解析即可：首先我们读取开头的 4 字节并将它们转换成 `uint32` 来获取消息的**长度**，之后我们再读取一个字节来获取 **ID**，最后剩余的字节做为**载荷**。

```go
// Read 函数从流中获取一条消息，返回值为空是代表消息类型是 keep-alive
func Read(r io.Reader) (*Message, error) {
    lengthBuf := make([]byte, 4)
    _, err := io.ReadFull(r, lengthBuf)
    if err != nil {
        return nil, err
    }
    length := binary.BigEndian.Uint32(lengthBuf)

    // keep-alive message
    if length == 0 {
        return nil, nil
    }

    messageBuf := make([]byte, length)
    _, err = io.ReadFull(r, messageBuf)
    if err != nil {
        return nil, err
    }

    m := Message{
        ID:      messageID(messageBuf[0]),
        Payload: messageBuf[1:],
    }

    return &m, nil
}
```

### Bitfields

有一种特别的消息类型被称为 **bitfield**，它是一个 peer 用来编码他拥有发送哪些 pieces 的数据结构。bitfield 看起来就像一个字节数组，我们只需要看哪些*比特位*被置为 1 就能知道这个 peer 有哪些我们需要的 pieces。你可以把 bitfield 理解为咖啡店的会员卡，开始我们只有一张全为 0 的空白卡，之后那些被“使用过”的位置就被翻转为 1。

以比特位而非字节为单位来表示数据使得这个数据结构相当紧凑。我们可以在一个字节的空间里表示 8 个数据——也就是 `bool` 数据类型。但是这也导致访问值变得比较困难，因为电脑最小的内存单位是字节，所以为了得到单个比特位上的值，我们必须进行一些二进制运算：

```go
// Bitfield 表示一个 peer 拥有的 pieces
type Bitfield []byte

// HasPiece 函数告诉我们某个 bitfield 是否有特定的 pieces
func (bf Bitfield) HasPiece(index int) bool {
    byteIndex := index / 8
    offset := index % 8
    return bf[byteIndex]>>(7-offset)&1 != 0
}

// SetPiece 把 bitfield 中的某一位置 1
func (bf Bitfield) SetPiece(index int) {
    byteIndex := index / 8
    offset := index % 8
    bf[byteIndex] |= 1 << (7 - offset)
}
```

## 全部组合到一块儿

我们现在有了一切下载 torrent 所需的一切信息：我们拥有从 tracker 那里获取的 peers 列表，并且我们可以使用 TCP 连接来与执行握手并发送和接收消息。我们最后的两个大问题是如何处理同时与多个 peers 进行通信时的**并发**，以及管理这些正在与我们进行交互的 peers 的**状态**。这两个问题都是传统的困难问题。

## 并发管理：把 channels 当成队列

在 Go 当中，我们可以[利用通信来管理内存](https://blog.golang.org/codelab-share)，并且我们可以把 Go channels 理解成一个方便的线程安全的队列。

我们将建立两个 channels 来同步我们的并发 worker：第一个用于分配不同的 peers 的工作（也就是需要下载的pieces），另一个用于收集已下载的 pieces。我们可以把已下载的 pieces 复制到缓存中并将它们组装成完整的文件。

```go
// 初始化用于分配工作和接收结果的队列
workQueue := make(chan *pieceWork, len(t.PieceHashes))
results := make(chan *pieceResult)
for index, hash := range t.PieceHashes {
    length := t.calculatePieceSize(index)
    workQueue <- &pieceWork{index, hash, length}
}

// 启动 workers
for _, peer := range t.Peers {
    go t.startDownloadWorker(peer, workQueue, results)
}

// 将结构收集到缓存中知道缓存满
buf := make([]byte, t.Length)
donePieces := 0
for donePieces < len(t.PieceHashes) {
    res := <-results
    begin, end := t.calculateBoundsForPiece(res.index)
    copy(buf[begin:end], res.buf)
    donePieces++
}
close(workQueue)
```

我们对每个从 tracker 那里收到的 peer 都生成一个 goroutine，它会连接并与 peer 握手，随后从 `workQueue` 中收集工作并尝试下载，最后将已下载的片段发送到 `results` channel 中。

```go
func (t *Torrent) startDownloadWorker(peer peers.Peer, workQueue chan *pieceWork, results chan *pieceResult) {
    c, err := client.New(peer, t.PeerID, t.InfoHash)
    if err != nil {
        log.Printf("Could not handshake with %s. Disconnecting\n", peer.IP)
        return
    }
    defer c.Conn.Close()
    log.Printf("Completed handshake with %s\n", peer.IP)

    c.SendUnchoke()
    c.SendInterested()

    for pw := range workQueue {
        if !c.Bitfield.HasPiece(pw.index) {
            workQueue <- pw // 如果当前 peer 没有所需的片段，就将片段放回 channel 中
            continue
        }

        // 下载 piece
        buf, err := attemptDownloadPiece(c, pw)
        if err != nil {
            log.Println("Exiting", err)
            workQueue <- pw // 将片段放回 channel 中，稍后再次尝试
            return
        }

        err = checkIntegrity(pw, buf)
        if err != nil {
            log.Printf("Piece #%d failed integrity check\n", pw.index)
            workQueue <- pw // 将片段放回 channel 中，稍后再次尝试
            continue
        }

        c.SendHave(pw.index)
        results <- &pieceResult{pw.index, buf} // 写入下载结果
    }
}
```

### 状态管理

我们会用一个结构体来跟踪每一个 peer，并且在我们收到对应消息时修改这个结构体。它包含我们从指定 peer 那里下载了哪些数据、我们向指定 peer 请求了哪些数据和我们是否处于 choked 状态等等信息。如果我们想进一步地测量它，我们可以使用有限状态机来结构化表示，不过目前结构体加上 switch 语句已经够用了。

```go
type pieceProgress struct {
    index      int
    client     *client.Client
    buf        []byte
    downloaded int
    requested  int
    backlog    int
}

func (state *pieceProgress) readMessage() error {
    msg, err := state.client.Read() // 请求消息
    switch msg.ID {
    case message.MsgUnchoke:
        state.client.Choked = false
    case message.MsgChoke:
        state.client.Choked = true
    case message.MsgHave:
        index, err := message.ParseHave(msg)
        state.client.Bitfield.SetPiece(index)
    case message.MsgPiece:
        n, err := message.ParsePiece(state.index, state.buf, msg)
        state.downloaded += n
        state.backlog--
    }
    return nil
}
```

### 发送请求

文件、pieces 和 piece 的哈希值并不是全部——我们可以进一步地把 pieces 分解成 **blocks**，一个 block 是 piece 的一部分，我们可以用 piece 的**序号**、piece 内的字节**偏移量**和 block 的长度来唯一定义一个 block。当我们向 peers 请求数据时，我们实际上是在请求 *blocks*。一个 block 的大小通常为 16KB，意味着 256KB 的 piece 可能需要 16 次请求来完成下载。

当 peer 收到大于 16KB 的 block 的请求时，他应当关闭连接。但是根据我的经验，peers 实际上对最大 128KB 的请求都是接受的。当我使用较大的 block 时得到的总速度提升并不明显，所以最还好是遵循 16KB 的规格。

### 管道化

网络消息的交换开销非常大，每次请求一个 block 会让我们的下载变得非常慢。因此，**管道化**我们的请求是非常重要的，它能使得我们同时维护一定数量的未满足的请求。这种技术可以将我们的连接的通量提升一个数量级。

传统来讲，BitTorrent 客户端维护一个包含 5 个管道化的请求的队列，这也是我所使用的方案。我发现这种方案能够让下载速度加倍。新的客户端则使用[动态大小的队列](https://luminarys.com/posts/writing-a-bittorrent-client.html)来更好地适应现代网络的速度和条件。实际上，队列的大小是一个非常值得调整且易于调整的参数，对于未来的性能优化有着重要的意义。

```go
// MaxBlockSize 是一个 request 所能请求的最大字节数
const MaxBlockSize = 16384

// MaxBacklog 是管道中最多存储的未完成的请求数量
const MaxBacklog = 5

func attemptDownloadPiece(c *client.Client, pw *pieceWork) ([]byte, error) {
    state := pieceProgress{
        index:  pw.index,
        client: c,
        buf:    make([]byte, pw.length),
    }

    // 设置 deadline 能使得没有应答的 peers 进入非堵塞状态
    // 30s 对于下载 262KB 的 piece 来说绰绰有余
    c.Conn.SetDeadline(time.Now().Add(30 * time.Second))
    defer c.Conn.SetDeadline(time.Time{}) // 清除 deadline

    for state.downloaded < pw.length {
        //  如果处于 unchoked 状态，就发送 request 直到管道已满
        if !state.client.Choked {
            for state.backlog < MaxBacklog && state.requested < pw.length {
                blockSize := MaxBlockSize
                // 最后一个 block 可能比典型的 block 要小一些
                if pw.length-state.requested < blockSize {
                    blockSize = pw.length - state.requested
                }

                err := c.SendRequest(pw.index, state.requested, blockSize)
                if err != nil {
                    return nil, err
                }
                state.backlog++
                state.requested += blockSize
            }
        }

        err := state.readMessage()
        if err != nil {
            return nil, err
        }
    }

    return state.buf, nil
}
```

### main.go

非常简短，我们即将抵达终点。

```go
package main

import (
    "log"
    "os"

    "github.com/veggiedefender/torrent-client/torrentfile"
)

func main() {
    inPath := os.Args[1]
    outPath := os.Args[2]

    tf, err := torrentfile.Open(inPath)
    if err != nil {
        log.Fatal(err)
    }

    err = tf.DownloadToFile(outPath)
    if err != nil {
        log.Fatal(err)
    }
}
```

# 这并非全部

为了文章的简介，我只介绍了一部分重要的代码片段。注意，我省去了 glue code、格式转换、单元测试和构建结构体的无聊部分。如果你感兴趣的话，你可以在[这里](https://github.com/veggiedefender/torrent-client)找到完整实现。