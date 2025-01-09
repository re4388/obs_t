[[kid_vid]]


整理為如下格式
```

└─> cat a1.txt
file 'emo1.mp4'
file 'emo2.mp4'
file 'emo3.mp4'
file 'emo4.mp4'
file 'emo5.mp4'
file 'emo6.mp4'


```


指令
ffmpeg -f concat -safe 0 -i a1.txt -c copy emoAll.mp4



mac side
use share to acer

acer side
`sudo tailscale file get .`



maybe can try this?
[GitHub - mifi/lossless-cut: The swiss army knife of lossless video/audio editing](https://github.com/mifi/lossless-cut)