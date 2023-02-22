# 固定场景实时选中跟踪

## 数据结构

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