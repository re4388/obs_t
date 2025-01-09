- related




加解密 and encode/decode
- 雙向
- encode/decode
	- hex(1~9a~f) and base64(a-z, A-Z, 0-9, =+)
	- base64
		- base62 + 兩個特殊字符（+ 和 /），並且通常會使用 = 來填充。
		- HTTP 請求中傳遞圖像或文件，會把這些 binary 透過 base64 轉為文本格式
		- 壓縮率比 hex, binary高多了(64 vs 16 vs 2)
		- 通用於網路，不包括非法字元
			- 非法字元?
				- 控制字符：如 \n、\r、\t 等，這些字符可能會干擾網絡協議或是系統的正常運作
				- 特殊字符：如 <、>、& 等，這些字符可能會被解釋為 HTML 標籤或是其他特殊語法
				- 非 ASCII 字符：如中文、日文等，這些字符可能會因為編碼問題而導致傳輸錯誤
		- [[how base64 work in details]]
	- base62
		- 大小寫字母（A-Za-z), 數字（0-9），共62個字符
		- avoids special characters (like +, /, or = in Base64), 因此在 URL 中使用時不需要進行額外的編碼和escaping，更適合用於生成 shortURL
	- URL 編碼（Percent Encoding）
		- 將 URL 中的特殊字符轉換為可傳輸的格式。
		- 使用百分號（%）後跟兩位十六進制數字表示字符。例如，空格被編碼為 `%20`




加解密
- 加密就可以解密，也是雙向
- 常見的包括
- 對稱: like AES (fast, but need to transfer the secert)
	- [[對稱加密]]
- 非對稱: like RAS  (slow, but can trasnfer the public key)
	- [[非對稱加密]]




 hash
- 單向，因此系統/db可以存 hash 過後的 password, 且系統無法反過來知道 password
- 會加上 salt → 避免被查彩虹表破解
- 不要用ms5 → 有限時間可以被彩虹表破 → 至少用 sha256
- 如果 perf 沒有考慮，可以用 bycrpt, 可以 hash 疊加多次，這樣多一個維度，讓彩虹表更難破
-  [[cs61b Hashing]]
- [ notion - hash](https://www.notion.so/nture4388/hash-hashMap-32b2de7517d744a0928f328a4f458ac2?pvs=4)