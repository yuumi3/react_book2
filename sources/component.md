# コンポーネント

## 新しいプロジェクトを作成

~~~~shell
npx create-react-app jyanken --template cra-template-ey-office
cd jyanken
code .
npm start
~~~~

※ プロンプトは省略しました

## 最初のコード

* src/index.tsx (src¥index.tsx)

~~~js
import React, { useState } from 'react'
import ReactDOM from 'react-dom'
import './index.css'

const JyankeGamePage: React.FC = () => {
  const [human, setHuman] = useState<number>(0)
  const [computer, setComputer] = useState<number>(0)

  const pon = (humanHand: number) => {
    const computerHand = Math.floor(Math.random() * 3)
    setHuman(humanHand)
    setComputer(computerHand)
  }

  const judge = ():number =>  (computer - human + 3) % 3

  return (
    <>
      <h1>じゃんけん ポン！</h1>
      <JyankenBox actionPon={(te) => pon(te)} />
      <ScoreBox human={human} computer={computer} judgment={judge()} />
    </>
  )
}

type JyankenBoxProps = {
  actionPon: (humanHand: number) => void
}
const JyankenBox: React.FC<JyankenBoxProps> = (props) => {
  return (
    <div>
      <button onClick={() => props.actionPon(0)}>グー</button>
      <button onClick={() => props.actionPon(1)}>チョキ</button>
      <button onClick={() => props.actionPon(2)}>パー</button>
    </div>
  )
}

type ScoreBoxProps = {
  human: number
  computer: number
  judgment: number
}
const ScoreBox: React.FC<ScoreBoxProps> = (props) => {
  const teString = ["グー","チョキ", "パー"]
  const judgmentString = ["引き分け","勝ち", "負け"]
  return (
    <table>
      <tbody>
        <tr><th>あなた</th><td>{teString[props.human]}</td></tr>
        <tr><th>Computer</th><td>{teString[props.computer]}</td></tr>
        <tr><th>勝敗</th><td>{judgmentString[props.judgment]}</td></tr>
      </tbody>
    </table>
  )
}


ReactDOM.render(<JyankeGamePage />, document.getElementById('root'))
~~~

* src/index.css (src¥index.css)

~~~css
div#root { padding: 10px; }
button { margin: 0 10px; }
table { margin-top: 20px; }
th { font-weight: 400; text-align: left; }
~~~


## フォーム

### Controlled Components

* src/index.tsx  (src¥index.tsx)

~~~js
import React, { useState, useEffect } from 'react'
import ReactDOM from 'react-dom'
import './index.css'

type BookType = {
  date: string
  item: string
  amount: number
}

const MoneyBook: React.FC = () => {
  const [books, setBooks] = useState<BookType[]>([])

  useEffect(() => {
    setBooks([
        {date: "1/1", item: "お年玉", amount: 10000},
        {date: "1/3", item: "ケーキ", amount: -500},
        {date: "2/1", item: "小遣い", amount: 3000},
        {date: "2/5", item: "マンガ", amount: -600}])
  }, [])

  const addBook = (book: BookType) => {
    setBooks(books.concat(book))
  }

  return (
    <div>
      <Title>小遣い帳</Title>
      <MoneyBookList books={books} />
      <MoneyEntry add={(book) => addBook(book)} />
    </div>
  )
}
type MoneyBookListProps = {
  books: BookType[]
}
const MoneyBookList: React.FC<MoneyBookListProps> = (props) => {
  return (
    <table className="book">
      <thead>
        <tr><th>日付</th><th>項目</th><th>入金</th><th>出金</th></tr>
      </thead>
      <tbody>
        {props.books.map((book) =>
          <MoneyBookItem book={book} key={book.date + book.item} /> )}
      </tbody>
    </table>
  )
}

type MoneyBookItemProps = {
  book: BookType
}
const MoneyBookItem: React.FC<MoneyBookItemProps> = (props) => {
  const {date, item, amount} = props.book
  return (
    <tr>
      <td>{date}</td>
      <td>{item}</td>
      <td>{amount >= 0 ? amount : null}</td>
      <td>{amount < 0 ? -amount : null}</td>
     </tr>
  )
}

type MoneyEntryProps = {
  add: (book: BookType) => void
}
const MoneyEntry: React.FC<MoneyEntryProps> = (props) => {
  const [payingIn, setPayingIn] = useState<boolean>(true)
  const [date, setDate] = useState<string>("")
  const [item, setItem] = useState<string>("")
  const [amount, setAmount] = useState<string>("")

  const clearEntry = () => {
    setPayingIn(true); setDate(""); setItem(""); setAmount("")
  }
  const onClickSubmit = () => {
    const amountInOut = Number(amount) * (payingIn ? 1 : -1)
    const book = {date: date, item: item, amount: amountInOut}

    props.add(book)
    clearEntry()
  }
  return (
    <div className="entry">
      <fieldset>
        <legend>記帳</legend>
        <div>
          <input type="radio" checked={payingIn} onChange={() => setPayingIn(true)} /> 入金
          <input type="radio" checked={!payingIn} onChange={() => setPayingIn(false)} /> 出金
        </div>
        <div>日付: <input type="text" value={date} onChange={(e) => setDate(e.target.value)} placeholder="3/15" /> </div>
        <div>項目: <input type="text" value={item} onChange={(e) => setItem(e.target.value)} placeholder="おこづかい" /> </div>
        <div>金額: <input type="text" value={amount} onChange={(e) => setAmount(e.target.value)} placeholder="500" /> </div>
        <div> <input type="submit" value="追加" onClick={() => onClickSubmit()} /> </div>
      </fieldset>
    </div>
  )
}

const Title: React.FC = (props) => {
  return (<h1>{props.children}</h1>)
}


ReactDOM.render(<MoneyBook />, document.getElementById('root'))
~~~

* src/index.css (src¥index.css)

~~~css
.book {
  border-collapse: collapse;
  width: 300px;
}
.book th, .book td {
  border: solid 1px;
  padding: 1px 15px;
}
.entry {
  margin-top: 20px;
  width: 300px;
}
.entry input[type="submit"] {
  margin-top: 10px;
  width: 60px;
  font-size: 120%;
}
.entry input[type="radio"] {
  margin-left: 40px;
  margin-bottom: 10px;
}
~~~

### Uncontrolled Components

* src/index.tsx (src¥index.tsx)

~~~js
import React, { useState, useEffect, useRef } from 'react'
import ReactDOM from 'react-dom'
import './index.css'

type BookType = {
  date: string
  item: string
  amount: number
}

const MoneyBook: React.FC = () => {
  const [books, setBooks] = useState<BookType[]>([])

  useEffect(() => {
    setBooks([
        {date: "1/1", item: "お年玉", amount: 10000},
        {date: "1/3", item: "ケーキ", amount: -500},
        {date: "2/1", item: "小遣い", amount: 3000},
        {date: "2/5", item: "マンガ", amount: -600}])
  }, [])

  const addBook = (book: BookType) => {
    setBooks(books.concat(book))
  }

  return (
    <div>
      <Title>小遣い帳</Title>
      <MoneyBookList books={books} />
      <MoneyEntry add={(book) => addBook(book)} />
    </div>
  )
}
type MoneyBookListProps = {
  books: BookType[]
}
const MoneyBookList: React.FC<MoneyBookListProps> = (props) => {
  return (
    <table className="book">
      <thead>
        <tr><th>日付</th><th>項目</th><th>入金</th><th>出金</th></tr>
      </thead>
      <tbody>
        {props.books.map((book) =>
          <MoneyBookItem book={book} key={book.date + book.item} /> )}
      </tbody>
    </table>
  )
}

type MoneyBookItemProps = {
  book: BookType
}
const MoneyBookItem: React.FC<MoneyBookItemProps> = (props) => {
  const {date, item, amount} = props.book
  return (
    <tr>
      <td>{date}</td>
      <td>{item}</td>
      <td>{amount >= 0 ? amount : null}</td>
      <td>{amount < 0 ? -amount : null}</td>
     </tr>
  )
}

type MoneyEntryProps = {
  add: (book: BookType) => void
}
const MoneyEntry: React.FC<MoneyEntryProps> = (props) => {
  const payingIn = useRef<HTMLInputElement>(null)
  const date = useRef<HTMLInputElement>(null)
  const item =  useRef<HTMLInputElement>(null)
  const amount =  useRef<HTMLInputElement>(null)

  const clearEntry = () => {
    if (payingIn.current) payingIn.current.checked = true
    if (date.current)     date.current.value = ""
    if (item.current)     item.current.value = ""
    if (amount.current)   amount.current.value = ""
  }
  const onClickSubmit = () => {
    const amountInOut = Number(amount.current?.value ?? 0) * (payingIn.current?.checked ? 1 : -1)
    const book: BookType = {
      date: date.current?.value ?? "",
      item: item.current?.value ?? "",
      amount: amountInOut}

    props.add(book)
    clearEntry()
  }
  return (
    <div className="entry">
      <fieldset>
        <legend>記帳</legend>
        <div>
          <input type="radio" name="payInOut" defaultChecked ref={payingIn} /> 入金
          <input type="radio" name="payInOut" /> 出金
        </div>
        <div>日付: <input type="text" defaultValue="" ref={date}  placeholder="3/15" /> </div>
        <div>項目: <input type="text" defaultValue="" ref={item}  placeholder="おこづかい" /> </div>
        <div>金額: <input type="text" defaultValue="" ref={amount} placeholder="500" /> </div>
        <div> <input type="submit" value="追加" onClick={() => onClickSubmit()} /> </div>
      </fieldset>
    </div>
  )
}

const Title: React.FC = (props) => {
  return (<h1>{props.children}</h1>)
}


ReactDOM.render(<MoneyBook />, document.getElementById('root'))
~~~

* src/index.css は変更無し