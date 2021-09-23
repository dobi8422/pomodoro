# pomodoro
## Demo:
https://dobi8422.github.io/pomodoro/
![](https://i.postimg.cc/FFTvBCrJ/image.png)
![](https://i.postimg.cc/T2WWY0mH/3.png)
---
## 功能
番茄鐘計時，音樂，鬧鐘，切換主題(背景,按鈕顏色)
---
## 待優化
1. 避免重複內容登入
2. 使用json-server
3. 進度條(progress)
4. 拖曳功能
5. toast(增加畫面中央灰色動畫(關閉提醒、開啟提醒、新增待辦事項成功、更新音樂:...、...)
6.元件化(emit, event bus, ref, reactive)
---
## 新的問題，已解決
1. 刪除時 無法將doingName = ''(li,div上下層重複觸發)(@click.stop,@click.capture)=>資料刪除後如何將元素重新渲染(改變一項資料使畫面更新(可能是因為stop導致畫面沒更新))
2. 音源 :src 故障->requirse
3. 播放音樂ref 不能在v-if內
4. 切換音樂(重新載入音樂，this.$refs.audio.load()
5. !!深拷貝和淺拷貝!!(設定時間和撥放時間的綁定 this.now_time...)
6. 在:class使用變數:class="{[變數],判斷式}"
---