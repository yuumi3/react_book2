# JSXの基本

### 新しいプロジェクトの作成

~~~~shell
npx create-react-app money_book --template cra-template-ey-office
cd money_book
code .
npm start
~~~~

※ プロンプトは省略しました

### JSXを書いてみる(ほぼHTMLのコード)

* src/index.tsx (src¥index.tsx)

~~~js
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'

const MoneyBook: React.FC = () => {
  return (
    <div>
      <h1>小遣い帳</h1>
      <table className="book">
        <thead>
          <tr><th>日付</th><th>項目</th><th>入金</th><th>出金</th></tr>
        </thead>
        <tbody>
          <tr><td>1/1</td><td>お年玉</td><td>10000</td><td></td></tr>
          <tr><td>1/3</td><td>ケーキ</td><td></td><td>500</td></tr>
          <tr><td>2/1</td><td>小遣い</td><td>3000</td><td></td></tr>
          <tr><td>2/5</td><td>マンガ</td><td></td><td>600</td></tr>
        </tbody>
      </table>
    </div>
  )
}

ReactDOM.render(<MoneyBook />, document.getElementById('root'))
~~~

* src/index.css (src¥index.css)

~~~css
.book {
  border-collapse: collapse;
}
.book th, .book td {
  border: solid 1px;
  padding: 1px 15px;
}
~~~

### JSXに式(値)を埋め込む

~~~js
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'

type BookType = {
  date: string
  item: string
  amount: number
}
const MoneyBook: React.FC = () => {
  const books: BookType[] = [
    {date: "1/1", item: "お年玉", amount: 10000},
    {date: "1/3", item: "ケーキ", amount: -500},
    {date: "2/1", item: "小遣い", amount: 3000},
    {date: "2/5", item: "マンガ", amount: -600}]
   return (
    <div>
      <h1>小遣い帳</h1>
      <table className="book">
        <thead>
          <tr><th>日付</th><th>項目</th><th>入金</th><th>出金</th></tr>
        </thead>
        <tbody>
          <tr><td>{books[0].date}</td><td>{books[0].item}</td><td>{books[0].amount}</td><td></td></tr>
          <tr><td>{books[1].date}</td><td>{books[1].item}</td><td></td><td>{-books[1].amount}</td></tr>
          <tr><td>{books[2].date}</td><td>{books[2].item}</td><td>{books[2].amount}</td><td></td></tr>
          <tr><td>{books[3].date}</td><td>{books[3].item}</td><td></td><td>{-books[3].amount}</td></tr>
         </tbody>
      </table>
    </div>
  )
}

ReactDOM.render(<MoneyBook />, document.getElementById('root'))
~~~

### コンポーネントの分割

~~~js
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'

type BookType = {
  date: string
  item: string
  amount: number
}
const MoneyBook: React.FC = () => {
  const books: BookType[] = [
    {date: "1/1", item: "お年玉", amount: 10000},
    {date: "1/3", item: "ケーキ", amount: -500},
    {date: "2/1", item: "小遣い", amount: 3000},
    {date: "2/5", item: "マンガ", amount: -600}]
  return (
    <div>
      <h1>小遣い帳</h1>
      <table className="book">
        <thead>
          <tr><th>日付</th><th>項目</th><th>入金</th><th>出金</th></tr>
        </thead>
        <tbody>
          <MoneyBookItem book={books[0]} />
          <MoneyBookItem book={books[1]} />
          <MoneyBookItem book={books[2]} />
          <MoneyBookItem book={books[3]} />
        </tbody>
      </table>
    </div>
  )
}

type MoneyBookItemProps = {
  book: BookType
}
const MoneyBookItem: React.FC<MoneyBookItemProps> = (props) => {
  const {date, item, amount} = props.book
  if (amount > 0) {
    return (
      <tr>
        <td>{date}</td>
        <td>{item}</td>
        <td>{amount}</td>
        <td></td>
      </tr>
    )
  } else {
    return (
      <tr>
        <td>{date}</td>
        <td>{item}</td>
        <td></td>
        <td>{-amount}</td>
      </tr>
    )
  }
}

ReactDOM.render(<MoneyBook />, document.getElementById('root'))
~~~

### 条件演算子を使いJSXをコンパクトにする

~~~js
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'

type BookType = {
  date: string
  item: string
  amount: number
}
const MoneyBook: React.FC = () => {
  const books: BookType[] = [
    {date: "1/1", item: "お年玉", amount: 10000},
    {date: "1/3", item: "ケーキ", amount: -500},
    {date: "2/1", item: "小遣い", amount: 3000},
    {date: "2/5", item: "マンガ", amount: -600}]
  return (
    <div>
      <h1>小遣い帳</h1>
      <table className="book">
        <thead>
          <tr><th>日付</th><th>項目</th><th>入金</th><th>出金</th></tr>
        </thead>
        <tbody>
          <MoneyBookItem book={books[0]} />
          <MoneyBookItem book={books[1]} />
          <MoneyBookItem book={books[2]} />
          <MoneyBookItem book={books[3]} />
        </tbody>
      </table>
    </div>
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

ReactDOM.render(<MoneyBook />, document.getElementById('root'))
~~~

### 繰り返し

~~~js
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'

type BookType = {
  date: string
  item: string
  amount: number
}
const MoneyBook: React.FC = () => {
  const books: BookType[] = [
    {date: "1/1", item: "お年玉", amount: 10000},
    {date: "1/3", item: "ケーキ", amount: -500},
    {date: "2/1", item: "小遣い", amount: 3000},
    {date: "2/5", item: "マンガ", amount: -600}]
  return (
    <div>
      <h1>小遣い帳</h1>
      <table className="book">
        <thead>
          <tr><th>日付</th><th>項目</th><th>入金</th><th>出金</th></tr>
        </thead>
        <tbody>
          {books.map((book) =>
            <MoneyBookItem book={book} key={book.date + book.item} /> )}
         </tbody>
      </table>
    </div>
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

ReactDOM.render(<MoneyBook />, document.getElementById('root'))
~~~

### 子要素を扱うコンポーネント

~~~js
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'

type BookType = {
  date: string
  item: string
  amount: number
}
const MoneyBook: React.FC = () => {
  const books: BookType[] = [
    {date: "1/1", item: "お年玉", amount: 10000},
    {date: "1/3", item: "ケーキ", amount: -500},
    {date: "2/1", item: "小遣い", amount: 3000},
    {date: "2/5", item: "マンガ", amount: -600}]
   return (
     <div>
      <Title>小遣い帳</Title>
      <table className="book">
        <thead>
          <tr><th>日付</th><th>項目</th><th>入金</th><th>出金</th></tr>
        </thead>
        <tbody>
          {books.map((book) =>
            <MoneyBookItem book={book} key={book.date + book.item} /> )}
         </tbody>
      </table>
    </div>
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

const Title: React.FC = (props) => {
  return (<h1>{props.children}</h1>)
}

ReactDOM.render(
  <MoneyBook />,
  document.getElementById('root')
)

ReactDOM.render(<MoneyBook />, document.getElementById('root'))
~~~
