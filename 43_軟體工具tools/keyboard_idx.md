

related:
[[config_idx]]



# macOS

Undo: **CMD Z**  
Redo: **CMD SHIFT Z**


## note
alt+x -> textSnipper (2024_12_09_09_57_22)
alt+y
---
- 2024_03_30
    - intelliJ 有 macro可以用
    - 用 ctrl + \ 啟動/結束 record
- 2024_03_02
    - ^c, ^r → refactor
- 2024_02_28
    - use `alt+h` → run last cmd in integrated terminal
    - 熱鍵設定的想法
        - 不要第一時間用熱鍵，因為會打架，先可以用 滑鼠 gui 達到即可，除非一直需要，常常用，那才表示有需要用熱鍵
- 2024_02_25
    - 改用 r-cmd+z 來打開 ai, rc+h 違反人體工學 XD
- 2024_02_24
    - raycast
        - alt+p → global 的 打開 intelliJ 專案的鍵 via intellJ toolbox
        - space + right cmd 打開 → 充分使用 raycast
- 2024_02_17 release note
    - update google_v2_search
        - directly go to google search bar
            - see history search and auto complete
            - can directy paste url if I want to paste url
    - use iterm2 → hotkey window (alt+enter)
        - only use for qq/todo for now
        - a hotkey to help me join things down
- new way to define key
    cmd+k, cmd+s 的組合鍵 -> vscode `workbench. action.openGlobalKeybindings`
- 2023-12-14
    
    intelliJ
    
    f1→ action
    
    alt+e → find file
- 最近改的熱鍵 2023-12-11
    - alt+2 → show recent file
    - alt+e → show recent location
    - alt+b → show bookmark (alt+m 是mark bookmark)
    - f1 → go to files…


## Some principle
- 一些 Mac 原生的熱鍵，我是還滿想要維持用 cmd + x, like save, write, open...
- code related -> alt
- gloabl stuff -> kit(space+right cmd), ctrl+shift+sapce (raycast)




## Role for IDE and note
- what feats I like but Notion don’t provide → try to make notion can do
    - line go up/down
    - duplicate line
    
- what feat I like in Vim but idea don’t provide → try to make idea provide
    - mark
    - keyboard navigation on explorer
  
- Single source of truth principle
    - no two ide or two note to align
    - unless you have a good reason to do tha
    - and knowing that you need to take the cost of align two truth and maintain both of them
- idea
    - my main ide
    - 盡量都還在努力放在 notion, 一次到位
- notion
    - my main note
    - like pg
- vscode
    
    - just play around
    - no real role in my work/personal workflow
    - it’s okay to not working
    - no spend time on its tooling for now
    


## workflow
  
### 需要 stash…暫時存放工作
- alt+,
- 9
- 可以輸入stash name and 不要選 index (表示你要keep index, 就是如果你有新建例檔案. 會 keep 住)
- archive
    
    ```JavaScript
    1. alt+3
    2. cltr+s to find git stash save...
    3. give a meaningful name or time
    4. run it
    5. alt+9 to check the stached stuff
    ```
    
### check git graph
- use ctrl+shift+up/down to see dif
- intelliJ
    - [Datagrip list (check this vid DataGrip Introduction - YouTube)](https://www.notion.so/Datagrip-list-check-this-vid-DataGrip-Introduction-YouTube-5b61004d1eee4315b2f8f49302b97858?pvs=21)
- 處理opt(currently alt) + symbol 會產生 symbol → 用 maestro change mapping then use that mapping in IDE
    ![[IMG-keyboard_idx-20241003105311562.png|IMG-keyboard_idx-20241003105311562.png]]
    - use KM to remap alt+u → 另外一組
    - use 另外一組 to do format in code editor


## karabinar
- swap OPT and CMD
- capslock as cmd when using with other and esc when using alone



## 還沒加入熱鍵先用 gui 的部份
- active editor → soft wrap
- open in find tool window




## alt ( cmd key, I use karabinar to cmd ↔ alt)
- `` ` `` show all window in the same app
- 1
    - IDE project tool t
  
- 2
    - ~~show bookmarks~~
    - ~~find file~~
    - ~~recent locations~~
    - ~~vscode -> gloablly search text  or Search: Find in Files´´~~
    - Go to File... (vscode)
- 3
	- show terminal in IDE
	- reveal file in Obs
- 4, jump to matching bracket
- 5, 不好按 ==NO USE==
- 6 不好按 ==NO USE==
- 7 不好按 ==NO USE==
- 8, jump to source (用在 git 編輯後跳到原本的檔案，或是跳到外部檔案）
- 9 不好按 ==NO USE==
- 0 copy abs path
- - copy relative path
- = multiple curosor (clone caret below)
  
- q
    - go back (global use Mae 設定了)
- a
    - go forward (global use Mae 設定了)
- z
    - toggle folding
  
- w (global)
    - ~~google search via keyboard mastro (改用 raycast 的 google search, 因為有還有提示和歷史紀錄)
    - ~~space + alt → Kit
    - ~~shift+ctrl+space → raycast
    - ray cast ext: quick search  https://www.raycast.com/iwfan/quick-search
	    - 需要先選到 clipboard, 才可以搜尋
	    - 如果要選到字就可以搜尋，那變成要每一個 app 都要有自己的設定，那會變成有很多地方都要管理，另外可能也有某個app 沒有相關的設定。
	    - 因此需要多按一下 ^-C 可能是一個折衷
- s
    - ~~IDE search text in file
    - ~~vscode: Search: Quick Search
    - vscode: Search: Find in Files
- x
    - ~~close all tab~~
    - workbench.files.action.showActiveFileInExplorer or `File: Reveal Active File in Explorer View`
- e
    - ~~search everywhere , go to files~~
    - ~~show recent file~~
    - ~~recent location~~
    - go to file (idea)
    - Go to File... (vscode)
- d
    - go to declaration (idea) go to definition (vscode)
- c
    - console.log below (intelliJ use 套件 logit, vscode use `print it`)
  
- r
    - go to usage, go to reference
- f
    - quick fix (show context Actions in IntelliJ)
- v
    - pure paste
- t
    - go to type declaration → 有點不好按, 很少用
- g
    - intelli `Run`, 常用於ts-typescript project, quick prototype, bun run 等
    - vscode → run last command
- b
    - show bookmark (alt+m 是mark bookmark)
- y →
本來是 terminal 切換 -> 先改為 alt + /
    - ~~run something, 看 IDE 配置, 目前讓他跑 run ts code, 需要跑一次才會生效~~
    - ~~目前改為 啟動 live template~~
    - ~~recent location~~
- h
    - ~~==NO USE==~~ ~~, → use action, hide all tool window~~
    - recent file
    - focus on chat gpt
- n
    - structure (show structure window at middle)
- u
    - format code (vscode: format document)
- j (global)
    - ()
- m
    - toggle book mark
- i
    - code complete basic (顯示 code complete的意思）, trigger suggestion (vscode)
- k (global)
    - [ ]
- ,
    - show VCS, like git stash, unstack, commit…etc
- o
    - open
- `l` (global)
    - { }
- `.`
    - show git graph
  
- `\`
    - open terminal in current folder. , 看 IDE 配置,
- p (global)
    - go to project window
    - ray-cast → open jetbrain toolbar project recent window
- `;` (global)
    - ~~global clipboard **Maccy**
    - 先改用raycast 內建的  ca

- `/` (global)
    - ~~iterm2 hotkey window~~
        ![[IMG-keyboard_idx-20241003105313891.png|IMG-keyboard_idx-20241003105313891.png]]
     -> terminal 切換，但少用?
![[IMG-keyboard_idx-20241003105313993.png]] 
	 

- `[` (global)
    - home
- `‘` ==NO USE==
- `]` (global)
    - end
  
- `up`/`down`, move line up down
- `alt+cmd+up/down`, copy line down (duplicate line or selection)





## cmd (mac 常用功能鍵，小心覆蓋到default功能， like save, copy..等等）

> ==(caps lock is also cmd via karabinar)==
- 0~9 use IDEA bo==okmark/mnimonic system==

- i -> trigger copilot inline suggesiton
- j -> join
	- use mastr to map cmd+j -> shift+ctrl +j, and vscode use shift+ctrl+j

- q, exit(default)
- a, select all (default)
- z, undo(default)
  
- w, close tab(default)
- s, save(default)
- x, cut(default)
  
  
- e, toggle bookmark
- d, add selection for next occurrence (vscode:default, need add it in intelliJ)
    - 瀏覽器 copy tab → 使用套件 duplicate tab
- c, copy(default)
- r, refresh(default)
- f, find(default)
- v, paste(default)
- enter, start new line(intelliJ) , （vscode內建)
- d, add selection for next Occurrence(intelliJ) , （vscode內建)
- 2, debug
- up, extend selection(intelliJ)
- down, shrink selection(intelliJ)
- t
- b (bold for text in markown or in many editor)
- k, 用 f1 action 取代, go to line
- l, 用 f1 action 取代, go to line
- up and down, expand region and undo expand region(vscode)
## functional key (可能需要看一下才可以按到, 除了 f1~f2)
- f1
    - menu(vscode default), ~~Find Action… (intelliJ)~~
    - ~~go to files…~~
    - find action
- f2 rename
  

- f3
	- 拍照
	- select area screenshot `Xnip`
    - try use `snipaste`
    - try use cleanShot x → 預設自動打開 annotation-tool→ 可以編輯
- f4
	- 拍照
	- full screenshot mac 內建
    - use cleanShot x → 預設自動打開 annotation-tool(只能跟上面綁定)→ ctrl+w關掉
    
 - f5 
	 - ~~intelliJ: compare files
	 - cleanShot -> capture and pin to screen
- f6 → open wemo chat at 2nd monitor
- f7, show emoji
- f8 extract method
- f9 intelliJ rollback
- f10 no use
  
聲音
- f10~f13: follow the keyboard symbol (mini MX)
  
## ctrl+shift
- l
    - raycast → lock screen
- up/down see commit dif
- left/right next tab, previous tab
- p 沉浸翻譯 in chrome
    
    ![[IMG-keyboard_idx-20241003105316092.png|IMG-keyboard_idx-20241003105316092.png]]
    
- k
    - extension keyboard
- b
    - bitward
- e → 處理 symbol 問題
- i → 同上
- u → 同上
- d → render all comment
- n → floating window
- s → js string template, apple → ${apple} (Kit)
- archived (probably due to not use frenquently use)
    - q → context info (intelliJ, 看當下的脈絡，method, 如果看不到的話）
    - s → surround with
## ctrl `^`
- /  -> keyboard setting shoftcut in vscode
- w (rotate window) → 沒用了
- g, vcs operation popup → 沒用了
- `\`
    - ~~[Finda](https://keminglabs.com/finda/setup/#visual-studio-code)~~
    - ~~hide/unhide 關掉的menu bar icon~~
    - 開啟/結束 intelliJ 的 macro
## opt+cmd
- / → next display via spectacle
- ← switch to left via spectacle
- ← switch to right via spectacle
- on complete
    - tab, next completion Option
    - shift + tab, previous completion Option
  

## vscode 複合鍵
- cmdQ, cmdQ -> quokka on the current file


## 瀏覽器環境特殊
- q → increase speed
- a → decrease speed




## obsidian 特化處理
- 因為 obs 沒有內建 go to top and go to down 的熱鍵，讓我 remap
- 目前解法就是用 maestro
	- 但是上下又會和其他 app 衝到
	- 因此 obs 自己 duplicate global 設定
	- 另外 alt + 左右中框號自己 reamp