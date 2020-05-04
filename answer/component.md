# コンポーネント

## BMI判定をしてくれるアプリ

~~~js
import React, { useState } from 'react'
import ReactDOM from 'react-dom'

const Bmi: React.FC = () => {
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

ReactDOM.render(<Bmi />, document.getElementById('root'))
~~~
