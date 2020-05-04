# JSX

## 残高の表示を追加してみて下さい

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
  let sum = 0
  const totals = books.map(book => sum += book.amount)
  return (
    <div>
      <Title>小遣い帳</Title>
      <table className="book">
        <thead>
          <tr><th>日付</th><th>項目</th><th>入金</th><th>出金</th><th>残高</th></tr>
        </thead>
        <tbody>
          {books.map((book, ix) =>
            <MoneyBookItem book={book} total={totals[ix]} key={book.date + book.item} /> )}
         </tbody>
      </table>
    </div>
  )
}

type MoneyBookItemType = {
  book: BookType
  total: number
}
const MoneyBookItem: React.FC<MoneyBookItemType> = (props) => {
  const {date, item, amount} = props.book
  return (
    <tr>
      <td>{date}</td>
      <td>{item}</td>
      <td>{amount >= 0 ? amount : null}</td>
      <td>{amount < 0 ? -amount : null}</td>
      <td>{props.total}</td>
     </tr>
  )
}

const Title: React.FC = (props) => {
  return (<h1>{props.children}</h1>)
}


ReactDOM.render(<MoneyBook />, document.getElementById('root'))
~~~
