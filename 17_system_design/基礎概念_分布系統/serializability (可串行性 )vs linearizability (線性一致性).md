- **serializability 可串行性
	- isolation property of transactions
	- every transaction may read and write multiple objects (rows, documents, records)
		- see “Single- Object and Multi-Object Operations” on page 228 in DDIA.
	- It guarantees that transactions behave the same as if they had executed in some serial order
		- each transaction running to completion before the next transaction starts
	    - 所以叫做 serial 一樣，一個一個來
    - 基本上就是不會有並行問題
    - 概念上如同 ACID 的 I, isolation, 多並行下，不會互相影響
	    - [[ACID]]
- **linearizability**  線性一致性
	- a recency guarantee on reads and writes of a register (an individual object)
	- It doesn’t group operations together into transactions, so it does not prevent problems such as **write skew** (see “Write Skew and Phantoms” on page 246), unless you take additional measures such as **materializing conflicts** (see “Materializing conflicts” on page 251).
    - 簡單說就是 寫入是立即的
    - 就是 CAP 的 C, consistency (不是"最終一致")
	    - [[分布式系統 CAP]]

資料庫可以兩種保證都提供，組合起來，又叫做 **strict serializability** or **strong one-copy serializability** (strong-1SR) [4, 13]. 我們可以用  two-phase locking (see “Two-Phase Lock‐ing (2PL)” on page 257) or actual serial execution (see “Actual Serial Execution” on page 252) 來實作 serializability + linearizability。

另外一種常見的 **serializable snapshot isolation** (see “Serializable Snapshot Isolation (SSI)” on page 261) 沒有 linearizability 保證。By design, **SSI makes reads from a consistent snapshot to avoid lock contention** between readers and writers. The whole point of a consistent snapshot is that **it does not include writes** that are more recent than the snapshot, and thus reads from the snapshot are not linearizable.


related:
- [[Consistency and Consensus, DDIA CHAPTER 9]]