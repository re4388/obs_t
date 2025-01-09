- related
	- [[cs61b Hashing]]
	- [hash, hashMap in notion](https://www.notion.so/nture4388/hash-hashMap-32b2de7517d744a0928f328a4f458ac2?pvs=4)


property:
1. 確性性 (相對於 機率性) deterministic 
    - the same input shall always have the same output
2. 要夠快  fast
    - it is most used before some DB or key operation process, shall not be the bottleneck.
3. 均勻分布，或是不容易碰撞 collision resistance / evenly distributed
    1. If two unique samples of input data result in identical outputs, it’s known as a collision.
4. 不可逆  non-Invertible 
    - That is, if a hash value `h` is available, value `x` cannot be found, that `hash(x) = h`
