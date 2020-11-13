# テスティング

## Jyankenにクリアボタンを追加

#### 1. BMI判定関数のユニットテスト

* src/App.tsx （E2Eテスト対応も含んでいます）

~~~js
import React, { useState } from 'react'

export const jugeBMI = (height: number, weight: number): string => {
  const heightM = height / 100
  const bmi = weight / (heightM ** 2)

  if (bmi < 18.5) {
    return "やせ"
  } else if (bmi >= 25) {
    return "肥満"
  } else {
    return "標準"
  }
}

export const App: React.FC = () => {
  const [height, setHeight] = useState("")
  const [weight, setWeight] = useState("")
  const [judgeText, setJudgeText] = useState("")

  const jugeButton = () => {
    setJudgeText(jugeBMI( Number(height), Number(weight)))
  }

  const boxStyle = {width: 100, height: 50, border: "solid 1px #000"}

  return (
    <>
      <p> 身長：<input id="heightInput" type="text" value={height} placeholder="170"
                onChange={(e) => setHeight(e.target.value)} /></p>
      <p> 体重：<input id="weightInput" type="text" value={weight} placeholder="60"
                onChange={(e) => setWeight(e.target.value)} /></p>
      <p> <input id="bmiButton" type="submit" value="BMI" onClick={() => jugeButton()} /></p>
      <p> {judgeText ?  <div id="judgeText" style={boxStyle}>{judgeText}</div> : null} </p>
    </>
  )
}
~~~

* src/bmi.test.ts

~~~js
import { jugeBMI } from './App'

describe('jugeBMI', () => {

  test('身長:170、体重:60で "標準" が戻る', () => {
    expect(jugeBMI(170, 60)).toBe("標準")
  })
  test('身長:170、体重:40で "やせ" が戻る', () => {
    expect(jugeBMI(170, 40)).toBe("やせ")
  })
  test('身長:170、体重:80で "肥満" が戻る', () => {
    expect(jugeBMI(170, 80)).toBe("肥満")
  })

})
~~~


#### 2. BMI判定アプリのE2Eテスト

* puppeteerのインストール

~~~
npm install --save-dev puppeteer jest-puppeteer @types/puppeteer 
~~~~

* src/index.test.ts

~~~js
import puppeteer from 'puppeteer'

describe('BMI判定アプリ', () => {
  const URL = 'http://localhost:3000'
  let browser:puppeteer.Browser
  let page:puppeteer.Page

  beforeAll(async () => {
    browser = await puppeteer.launch({headless: true})
    page = await browser.newPage()
  })

  afterAll(async () => {
     await browser.close()
  })

  beforeEach(async() => {
    await page.goto(URL)
  })

  it('身長入力欄に170、体重入力欄に60を入力し、BMI（判定）ボタンを押すと結果に "標準" が表示される', async () => {
    await page.type('#heightInput', '170')
    await page.type('#weightInput', '60')
    await page.click('#bmiButton')

    const text = await page.$eval('#judgeText', (e: Element) => e.textContent)
    expect(text).toBe('標準')
  })

})
~~~
