# コンポーネント

## 起動時に、グー、グー、引き分けが表示されないようにする

* src/App.tsx

~~~js
import React, { useState } from 'react'
import './index.css'

export const App: React.FC = () => {
  const [human, setHuman] = useState<number>(0)
  const [computer, setComputer] = useState<number>(0)
  const [showScore, setShowScore] = useState<boolean>(false)

  const pon = (humanHand: number) => {
    const computerHand = Math.floor(Math.random() * 3)
    setHuman(humanHand)
    setComputer(computerHand)
    setShowScore(true)
  }

  const judge = ():number =>  (computer - human + 3) % 3

  return (
    <>
      <h1>じゃんけん ポン！</h1>
      <JyankenBox actionPon={(te) => pon(te)} />
      {showScore && <ScoreBox human={human} computer={computer} judgment={judge()} />}
    </>
  )
}

・・・以下は同じ・・・
~~~



## BMI判定をしてくれるアプリ

* src/App.tsx

~~~js
import React, { useState } from 'react'

export const App: React.FC = () => {
  const [height, setHeight] = useState("")
  const [weight, setWeight] = useState("")
  const [judgeText, setJudgeText] = useState("")

  const judgeBMI = () => {
    const heightM = Number(height) / 100
    const bmi = Number(weight) / (heightM ** 2)

    if (bmi < 18.5) {
      setJudgeText("やせ")
    } else if (bmi >= 25) {
      setJudgeText("肥満")
    } else {
      setJudgeText("標準")
    }
  }

  const boxStyle = {width: 100, height: 50, border: "solid 1px #000"}

  return (
    <>
      <p> 身長：<input type="text" value={height} placeholder="170" onChange={(e) => setHeight(e.target.value)} /></p>
      <p> 体重：<input type="text" value={weight} placeholder="60" onChange={(e) => setWeight(e.target.value)} /></p>
      <p> <input type="submit" value="BMI" onClick={() => judgeBMI()} /></p>
      <p> {judgeText ?  <div style={boxStyle}>{judgeText}</div> : null} </p>
    </>
  )
}
~~~
