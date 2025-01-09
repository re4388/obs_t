---  
alias:  
creation_date: 2024-06-05 19:37  
tags: 
related:
- [[_db_idx]] 


---  

uuid 的缺點
- 亂數, 不是人類可讀
- 人類可讀的好處?
	- 類似如果你設計如下id, 你可以設定 prefix 是一個特定的東西
	- 類似 sc_xxxxxxxxx, xxxxxxx 是亂數
	- sc 表示 scooter
	- 你一眼就可以知道這個 id 是哪種類型
	- 甚至可以透過這些 id 讓你進行一些monitor, 監控等等工具，因為你可以查找了


ref:
- [為人類設計 API：物件 ID - DEV 社區 --- Designing APIs for humans: Object IDs - DEV Community](https://dev.to/stripe/designing-apis-for-humans-object-ids-3o5a)




ts code example
```ts fold
import crypto from "crypto";

// https://news.ycombinator.com/item?id=40582365

function makeSlugV1(length: number): string {
  // 有效字元, 這些是我們要用來產生 slug 的字元
  const validChars =
    "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";

  // 用內建的 crypto.randomBytes 產生一個亂數 in bytes
  const randomBytes = crypto.randomBytes(length);

  let result = "";
  for (let i = 0; i < length; i++) {
    // 取出 randomBytes 中的一個 byte
    const randomByte = randomBytes[i];

    // 取餘數 確保 randomByte 在 validChars 範圍內
    // the valid chars string is 62 characters, so naively using a modulo on a random byte will technically introduce a bias (since dividing 256 values by 62 leaves a remainder). I don't expect it to really matter here, but since you're putting in the effort of using crypto.randomBytes I figured you might appreciate the nitpick ;). see https://www.pcg-random.org/posts/bounded-rands.html
    // or just use this https://github.com/paralleldrive/cuid2
    const idx = randomByte % validChars.length;

    // 從有效字元取出一個字元
    result += validChars[idx];
  }

  return result;
}

let a2 = makeSlugV1(5);

console.log("a2", a2);

// https://news.ycombinator.com/item?id=40583239
function makeSlugV2(length: number): string {
  const alphabet = "0123456789abcdefghjkmnpqrstvwxyz";

  let result = "";

  for (let i = 0; i < length; i++) {
    result += alphabet[crypto.randomInt(alphabet.length)];
  }

  return result;
}

type TABLE_NAME_TO_ID_SPEC = {
  [tableName: string]: {
    prefix: string;
    length: number;
  };
};

const table_name_to_id_spec: TABLE_NAME_TO_ID_SPEC = {
  table1: { prefix: "sc", length: 10 },
  table2: { prefix: "py", length: 10 },
  table3: { prefix: "jk", length: 10 },
};

// table_name_to_id_spec['table1'].prefix
function makeId(tableName: string): string {
  const idSpec = table_name_to_id_spec[tableName];
  console.log("idSpec", idSpec);
  const prefix = idSpec.prefix;
  const slugLength = idSpec.length - prefix.length - 1;
  return `${prefix}_${makeSlugV2(slugLength)}`;
}

const res1 = makeId("table1");
console.log("res1", res1);

```