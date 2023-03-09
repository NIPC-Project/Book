# 视频+跟踪

## 数据结构

### 固定场景下单目标实时跟踪算法在 NIPC-5 开发板上的实现



### 固定场景下单个运动物体的实时跟踪

```swift
struct Frame {
  // 只使用灰度进行计算
  var data: FixedLengthArray<Byte>(1080*1920)
}

struct Object {
  var position: Position
  // 只记录物体的中心位置
  struct Position {
    var x: Uint // [0, 1080-1]
    var y: Uint // [0, 1920-1]
  }
}

struct Input {
  var frame: Frame
}

struct Output {
  var frame: Frame
  // 一帧中可能有物体也可能没有
  var object: Object?
}

struct Model {
  // 背景的默认值设置为全黑就好了 后面会自动更新为真实的背景
  var background: Frame
  // 只保留上一帧的信息
  var previousFrame: Frame
  var previousObject: Object?

  // 每40ms从摄像头拿到新的一帧时调用
  func runAlgotithm(input: Input) -> Output {
    let currentFrame: Frame = input.frame

    let backgroundChanged: Bool = detectBackgroundChange(currentFrame: currentFrame, backgroud: self.background, previousFrame: self.previousFrame)
    if backgroundChanged {
      self.background = currentFrame
    } else {
      let currentObject: Object? = detectObject(currentFrame: currentFrame, backgroud: self.background, previousFrame: self.previousFrame, previousObject: self.previousObject)
    }

    self.previousFrame = currentFrame
    self.previousObject = currentObject
    let output = Output(frame: currentFrame, object: currentObject)
    return Output(state: currentState)
  }

  func detectBackgroundChange(currentFrame: Frame, backgroud: Frame, previousFrame: Frame) -> Bool {
    // 如果画面剧烈变化/整体变化，说明背景发生变化
    if backgroundChanged {
      return true
    } else {
      return false  
    }
  }

  func detectObject(currentFrame: Frame, backgroud: Frame, previousFrame: Frame, previousObject: Object?) -> Object? {
    if hasObject {
      return Object(position: Position(x,y))
    } else {
      return null
    }
  }
}
```

### 固定场景下多个运动物体的实时选中与跟踪

```swift
struct Frame {
  var data: FixedLengthArray<Bit>(1080*1920*16)
}

struct Object {
  var id: Uint
  var position: Position
  struct Position {
    var x: Uint
    var y: Uint
    var w: Uint
    var h: Uint
  }
}

struct State {
  var frame: Frame
  var objects: Array<Object>
}

struct Input {
  var frame: Frame
}

struct Output {
  var state: State
}

struct Model {
  static let HISTORY_COUNT = 5
  static var id: Uint // 每发现一个新物体 id 加一  数值上等于从最开始检测到的物体总数

  var historyStates: FixedLengthArray<State>(HISTORY_COUNT)

  // 每40ms从摄像头拿到新的一帧时调用
  func runAlgotithm(input: Input) -> Output {
    let currentFrame: Frame = input.frame

    var currentObjects: Array<Object> = []
    // 将 self.historyStates 与 currentFrame 对比
    //   如果有新进入场景的 Object，新建这个 Object（新id，Self.id++）并在 currentObjects 中添加
    //   如果有原本就在场景中的 Object，根据 self.historyStatess 新建 Object（同id）并在 currentObjects 中添加

    // 使用 currentObjects 更新 state.historyObjects
    let currentState: State = State(frame: currentFrame, objects: currentObjects)
    state.historyStates.removeFirst()
    state.historyStates.append(currentState)

    // 返回当前帧和当前帧中的物体
    return Output(state: currentState)
  }
}
```
