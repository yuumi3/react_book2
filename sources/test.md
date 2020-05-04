# テスティング

## ユニットテスト (Unit test)

### Jyakneクラスの変更

* src/Jyanken.ts  (src¥Jyanken.ts)

~~~js
export enum Te { Guu, Choki, Paa }
export enum Judgment {Draw, Win, Lose}
export interface Score {human: Te, computer: Te, created_at: Date, judgment: Judgment}
export interface Statuses {draw: number, win: number, lose: number}

export default class Jyanken {
  scores: Score[]
  statuses: number[]

  constructor() {
    this.scores = []
    this.statuses = [0, 0, 0]
  }
  pon(human_hand:number, computer_hand:number = Math.floor(Math.random() * 3)) : void {
    const judgment = (computer_hand - human_hand + 3) % 3
    this.scores.push({human: human_hand, computer: computer_hand, created_at: new Date(), judgment: judgment})
    this.statuses[judgment]++
  }
  getScores() : Score[] {
    return this.scores.slice().reverse()
  }
  getStatuses() : Statuses {
    return {draw: this.statuses[Judgment.Draw], win: this.statuses[Judgment.Win], lose: this.statuses[Judgment.Lose]}
  }
}
~~~

### テストコード

* src/Jyanke.test.ts  (src¥Jyanke.test.ts)

~~~js
import Jyanken from './Jyanken'

describe('Jyanken', () => {
  const jyanken = new Jyanken()

  describe('勝敗の判定が正しいか', () => {
    describe('コンピュターがグーの場合', () => {
      test('人間がグーなら引き分け', () => {
        jyanken.pon(0, 0)
        expect(jyanken.getScores()[0].judgment).toBe(0)
      })
      test('人間がチョキなら負け', () => {
        jyanken.pon(1, 0)
        expect(jyanken.getScores()[0].judgment).toBe(2)
      })
      test('人間がパーなら勝ち', () => {
        jyanken.pon(2, 0)
        expect(jyanken.getScores()[0].judgment).toBe(1)
      })
    })

    describe('コンピュターがチョキの場合', () => {
      test('人間がグーなら勝ち', () => {
        jyanken.pon(0, 1)
        expect(jyanken.getScores()[0].judgment).toBe(1)
      })
      test('人間がチョキなら引き分け', () => {
        jyanken.pon(1, 1)
        expect(jyanken.getScores()[0].judgment).toBe(0)
      })
      test('人間がパーなら負け', () => {
        jyanken.pon(2, 1)
        expect(jyanken.getScores()[0].judgment).toBe(2)
      })
    })

    describe('コンピュターがパーの場合', () => {
      test('人間がグーなら負け', () => {
        jyanken.pon(0, 2)
        expect(jyanken.getScores()[0].judgment).toBe(2)
      })
      test('人間がチョキなら勝ち', () => {
        jyanken.pon(1, 2)
        expect(jyanken.getScores()[0].judgment).toBe(1)
      })
      test('人間がパーなら引き分け', () => {
        jyanken.pon(2, 2)
        expect(jyanken.getScores()[0].judgment).toBe(0)
      })
    })
  })
})
~~~

## E2Eテスト (End to End test)

### インストール

~~~shell
npm install --save-dev puppeteer jest-puppeteer @types/puppeteer
~~~

※ プロンプトは省略しました

### テストコードの書き方 (簡単なテストコード)

*  src/index.test.ts (src¥index.test.ts)

~~~js
import puppeteer from 'puppeteer'

describe('Jyanken', () => {
  let browser:puppeteer.Browser
  let page:puppeteer.Page

  beforeAll(async () => {
    browser = await puppeteer.launch({headless: true})
    page = await browser.newPage()
  })

  afterAll(async () => {
    await browser.close()
  })

  it('アクセスすると「じゃんけん ポン！」と表示されている', async () => {
    await page.goto('http://localhost:3000')
    const text = await page.$eval("h1", (e: Element) => e.textContent)
    expect(text).toMatch('じゃんけん ポン！')
  })
})
~~~

### アプリの変更

ここでは、じゃんけんの手のボタン、結果・成績切り替えタブにIDを追加しました。

* src/index.tsx(src¥index.tsx) のコードの変更 (1)

~~~js
const JyankenBox: React.FC<JyankenBoxProps> = (props) => {
  const style = {marginLeft: 20}
  return (
    <div style={{marginTop: 40, marginBottom: 30, marginLeft: 50}}>
      <Button variant="contained" id="btn-guu" ...>グー</Button>
      <Button variant="contained" id="btn-choki" ...}>チョキ</Button>
      <Button variant="contained" id="btn-paa" ...>パー</Button>
    </div>
  )
}
~~~

* src/index.tsx(src¥index.tsx) のコードの変更 (2)

~~~js
const JyankeGamePage: React.FC = () => {

  ・・・省略・・・

   return (
    <div style={{marginLeft: 30}}>
      <Header>じゃんけん ポン！</Header>
      <JyankenBox actionPon={(te) => pon(te)} />
      <Paper style={{width: 400}}>
        <Link id="tab-scores" ...>対戦結果</Button></Link>
        <Link id="tab-status" ...>対戦成績</Button></Link>

  ・・・省略・・・
~~~

### テストコード

* src/index.test.ts (src¥index.test.ts)

~~~js
import puppeteer from 'puppeteer'

describe('じゃんけんアプリ', () => {
  const URL = 'http://localhost:3000'
  let browser:puppeteer.Browser
  let page:puppeteer.Page

  const getTexts = (list: Element[]) => list.map(e => e.textContent)

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

  it('グーをクリックすると対戦が行われ、対戦結果が表示される', async () => {
    await page.click('#btn-guu')
    const texts = await page.$$eval("tbody td", getTexts)
    const [_, human, computer, judgment] = texts
    expect(human).toBe('グー')
    expect(computer).toMatch(/^(グー|チョキ|パー)$/)
    expect(judgment).toMatch(/^(勝ち|引き分け|負け)$/)
  })

  it('グーをクリックした後に対戦成績をクリックすると、対戦成績が表示される', async () => {
    await page.click('#btn-guu')
    await page.click('#tab-status')
    const texts = await page.$$eval("tbody td:nth-child(2n)", getTexts)
    const [win, lose, draw] = texts.map((e) => Number(e))
    expect(win >= 0 && win <= 1).toBeTruthy()
    expect(lose >= 0 && lose <= 1).toBeTruthy()
    expect(draw >= 0 && draw <= 1).toBeTruthy()
    expect(win + lose + draw).toBe(1)
  })

  it('2回クリックすると、対戦結果が2行表示される', async () => {
    await page.click('#btn-guu')
    await page.click('#btn-guu')
    const texts = await page.$$eval("tbody tr", getTexts)
    expect(texts.length).toBe(2)
  })

})
~~~

