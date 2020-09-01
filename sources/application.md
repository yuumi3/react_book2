# コンポーネントの応用

### Material-UI

JSXの基本で作った jyanken を改造

* インストール手順

~~~shell
npm install @material-ui/core
~~~

※ プロンプトは省略しました

* src/Jyanken.ts (src¥Jyanken.ts) の追加

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
  pon(human_hand:number) : void {
    const computer_hand = Math.floor(Math.random() * 3)
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

* src/index.tsx (src¥index.tsx)

~~~js
import React, { useState, useMemo } from 'react'
import ReactDOM from 'react-dom'
import Button from '@material-ui/core/Button'
import Paper from '@material-ui/core/Paper'
import Tabs from '@material-ui/core/Tabs'
import Tab from '@material-ui/core/Tab'
import Table from '@material-ui/core/Table'
import TableBody from '@material-ui/core/TableBody'
import TableCell from '@material-ui/core/TableCell'
import TableHead from '@material-ui/core/TableHead'
import TableRow from '@material-ui/core/TableRow'
import Jyanken, { Statuses, Score, Te, Judgment } from './Jyanken'

const JyankeGamePage: React.FC = () => {
  const [scores, setScores] = useState<Score[]>([])
  const [status, setStatus] = useState<Statuses>({draw: 0, win: 0, lose: 0})
  const [tabIndex, setTabIndex] = useState(0)
  const jyanken = useMemo(() => new Jyanken(), [])

  const tabChange = (ix: number) => {
    setTabIndex(ix)
    getResult()
  }
  const getResult = () => {
    setScores(jyanken.getScores())
    setStatus(jyanken.getStatuses())
  }
  const pon = (te: number) => {
    jyanken.pon(te)
    getResult()
  }

  const tabStyle = {width: 200, height: 50, color: '#fff', backgroundColor: '#01bcd4'}
  return (
      <div style={{marginLeft: 30}}>
        <Header>じゃんけん ポン！</Header>
        <JyankenBox actionPon={(te) => pon(te)} />
        <Paper style={{width: 400}}>
          <Tabs value={tabIndex} onChange={(_, ix) => tabChange(ix)}>
            <Tab label="対戦結果" value={0} style={tabStyle}/>
            <Tab label="対戦成績" value={1} style={tabStyle} />
          </Tabs>
          { tabIndex === 0 ? <ScoreList scores={scores} /> : null}
          { tabIndex === 1 ? <StatusBox status={status} /> : null}
        </Paper>
      </div>
  )
}

const Header: React.FC = (props) => (<h1>{props.children}</h1>)

const judgmentStyle = (judgment: Judgment) => (
  {paddingRight: 16, color: ["#000", "#2979FF", "#FF1744"][judgment]})

type StatusBoxProps = {
  status: Statuses
}
const StatusBox: React.FC<StatusBoxProps> = (props) => (
  <Table>
    <TableBody>
      <TableRow>
        <TableCell variant="head">勝ち</TableCell>
        <TableCell style={judgmentStyle(Judgment.Win)}>{props.status.win}</TableCell>
      </TableRow>
      <TableRow>
        <TableCell variant="head">負け</TableCell>
        <TableCell style={judgmentStyle(Judgment.Lose)}>{props.status.lose}</TableCell>
      </TableRow>
      <TableRow>
        <TableCell variant="head">引き分け</TableCell>
        <TableCell style={judgmentStyle(Judgment.Draw)}>{props.status.draw}</TableCell>
      </TableRow>
    </TableBody>
  </Table>
)

type JyankenBoxProps = {
  actionPon: (te: number) => void
}
const JyankenBox: React.FC<JyankenBoxProps> = (props) => {
  const style = {marginLeft: 20}
  return (
    <div style={{marginTop: 40, marginBottom: 30, marginLeft: 50}}>
      <Button variant="contained" id="btn-guu"
        onClick={() => props.actionPon(Te.Guu)} style={style}>グー</Button>
      <Button variant="contained" id="btn-choki"
        onClick={() => props.actionPon(Te.Choki)} style={style}>チョキ</Button>
      <Button variant="contained" id="btn-paa"
        onClick={() => props.actionPon(Te.Paa)} style={style}>パー</Button>
    </div>
  )
}

type ScoreListProps = {
  scores: Score[]
}
const ScoreList: React.FC<ScoreListProps> = (props) => (
  <Table>
    <TableHead>
      <TableRow>
        <TableCell>時間</TableCell><TableCell>人間</TableCell>
        <TableCell>コンピュータ</TableCell><TableCell>結果</TableCell>
      </TableRow>
    </TableHead>
    <TableBody>
      {props.scores.map((score, ix) => <ScoreListItem key={ix} score={score} />)}
    </TableBody>
  </Table>
)

type ScoreListItemProps = {
  score: Score
}
const ScoreListItem: React.FC<ScoreListItemProps> = (props) => {
  const teString = ["グー","チョキ", "パー"]
  const judgmentString = ["引き分け","勝ち", "負け"]
  const dateHHMMSS = (d: Date) => d.toTimeString().substr(0, 8)

  const {created_at, human, computer, judgment} = props.score
  return (
    <TableRow>
      <TableCell style={judgmentStyle(judgment)}>{dateHHMMSS(created_at)}</TableCell>
      <TableCell style={judgmentStyle(judgment)}>{teString[human]}</TableCell>
      <TableCell style={judgmentStyle(judgment)}>{teString[computer]}</TableCell>
      <TableCell style={judgmentStyle(judgment)}>{judgmentString[judgment]}</TableCell>
    </TableRow>
  )
}

ReactDOM.render(<JyankeGamePage/>, document.getElementById('root'))
~~~

* src/index.css (src¥index.css) ファイルは削除してもよいです

### React Router

* src/index.tsx (src¥index.tsx)

~~~js
import React, { useState, useMemo } from 'react'
import ReactDOM from 'react-dom'
import { BrowserRouter as Router,
  Switch, Route,  Redirect, Link, useLocation } from 'react-router-dom'
import Button from '@material-ui/core/Button'
import Paper from '@material-ui/core/Paper'
import Table from '@material-ui/core/Table'
import TableBody from '@material-ui/core/TableBody'
import TableCell from '@material-ui/core/TableCell'
import TableHead from '@material-ui/core/TableHead'
import TableRow from '@material-ui/core/TableRow'
import Jyanken, { Statuses, Score, Te, Judgment } from './Jyanken'

const JyankeGamePage: React.FC = () => {
  const [scores, setScores] = useState<Score[]>([])
  const [status, setStatus] = useState<Statuses>({draw: 0, win: 0, lose: 0})
  const jyanken = useMemo(() => new Jyanken(), [])

  const getResult = () => {
    setScores(jyanken.getScores())
    setStatus(jyanken.getStatuses())
  }
  const pon = (te: number) => {
    jyanken.pon(te)
    getResult()
  }

  let location = useLocation()
  const tabStyle = {width: 200, height: 50, color: '#fff', backgroundColor: '#01bcd4'}
  const activeStyle = (path: string) => {
    const borderBottomColor = location.pathname.match(path) ? "#f00" : "#01bcd4"
    return {...tabStyle,  ...{borderBottom: `solid 2px ${borderBottomColor}`}}
  }
  return (
    <div style={{marginLeft: 30}}>
      <Header>じゃんけん ポン！</Header>
      <JyankenBox actionPon={(te) => pon(te)} />
      <Paper style={{width: 400}}>
        <Link id="tab-scores" to="/scores" style={{textDecoration: 'none'}}><Button style={activeStyle('scores')}>対戦結果</Button></Link>
        <Link id="tab-status" to="/status" style={{textDecoration: 'none'}}><Button style={activeStyle('status')}>対戦成績</Button></Link>
        <Switch>
          <Route path="/scores" component={() => <ScoreList scores={scores} />}/>
          <Route path="/status" component={() => <StatusBox status={status} />}/>
          <Route exact path="/" component={() => <Redirect to="/scores" />}/>
        </Switch>
      </Paper>
    </div>
  )
}

const Header: React.FC = (props) => (<h1>{props.children}</h1>)

const judgmentStyle = (judgment: Judgment) => (
  {paddingRight: 16, color: ["#000", "#2979FF", "#FF1744"][judgment]})

type StatusBoxProps = {
  status: Statuses
}
const StatusBox: React.FC<StatusBoxProps> = (props) => (
  <Table>
    <TableBody>
      <TableRow>
        <TableCell variant="head">勝ち</TableCell><TableCell style={judgmentStyle(Judgment.Win)}>{props.status.win}</TableCell>
      </TableRow>
      <TableRow>
        <TableCell variant="head">負け</TableCell><TableCell style={judgmentStyle(Judgment.Lose)}>{props.status.lose}</TableCell>
      </TableRow>
      <TableRow>
        <TableCell variant="head">引き分け</TableCell><TableCell style={judgmentStyle(Judgment.Draw)}>{props.status.draw}</TableCell>
      </TableRow>
    </TableBody>
  </Table>
)

type JyankenBoxProps = {
  actionPon: (te: number) => void
}
const JyankenBox: React.FC<JyankenBoxProps> = (props) => {
  const style = {marginLeft: 20}
  return (
    <div style={{marginTop: 40, marginBottom: 30, marginLeft: 50}}>
      <Button variant="contained" id="btn-guu" onClick={() => props.actionPon(Te.Guu)} style={style}>グー</Button>
      <Button variant="contained" id="btn-choki" onClick={() => props.actionPon(Te.Choki)} style={style}>チョキ</Button>
      <Button variant="contained" id="btn-paa" onClick={() => props.actionPon(Te.Paa)} style={style}>パー</Button>
    </div>
  )
}

type ScoreListProps = {
  scores: Score[]
}
const ScoreList: React.FC<ScoreListProps> = (props) => (
  <Table>
    <TableHead>
      <TableRow>
        <TableCell>時間</TableCell><TableCell>人間</TableCell><TableCell>コンピュータ</TableCell><TableCell>結果</TableCell>
      </TableRow>
    </TableHead>
    <TableBody>
      {props.scores.map((score, ix) => <ScoreListItem key={ix} score={score} />)}
    </TableBody>
  </Table>
)

type ScoreListItemProps = {
  score: Score
}
const ScoreListItem: React.FC<ScoreListItemProps> = (props) => {
  const teString = ["グー","チョキ", "パー"]
  const judgmentString = ["引き分け","勝ち", "負け"]
  const dateHHMMSS = (d: Date) => d.toTimeString().substr(0, 8)

  const {created_at, human, computer, judgment} = props.score
  return (
    <TableRow>
      <TableCell style={judgmentStyle(judgment)}>{dateHHMMSS(created_at)}</TableCell>
      <TableCell style={judgmentStyle(judgment)}>{teString[human]}</TableCell>
      <TableCell style={judgmentStyle(judgment)}>{teString[computer]}</TableCell>
      <TableCell style={judgmentStyle(judgment)}>{judgmentString[judgment]}</TableCell>
    </TableRow>
  )
}

ReactDOM.render(
  <Router>
    <JyankeGamePage/>
  </Router>,
  document.getElementById('root')
)
~~~


## サーバーとの通信

#### weatherプロジェクトの作成

~~~~shell
npx create-react-app weather --template cra-template-ey-office
cd weather
npm install @material-ui/core
npm install @material-ui/icons
code .
npm start
~~~~

※ プロンプトは省略しました

#### コード


* src/index.tsx (src¥index.tsx)

~~~js
import React, { useState } from 'react'
import ReactDOM from 'react-dom'
import Card from '@material-ui/core/Card'
import CardHeader from '@material-ui/core/CardHeader'
import CardActions from '@material-ui/core/CardActions'
import CardContent from '@material-ui/core/CardContent'
import List from '@material-ui/core/List'
import ListItem from '@material-ui/core/ListItem'
import ListItemIcon from '@material-ui/core/ListItemIcon'
import ListItemText from '@material-ui/core/ListItemText'
import InputLabel from '@material-ui/core/InputLabel'
import MenuItem from '@material-ui/core/MenuItem'
import FormControl from '@material-ui/core/FormControl'
import Select from '@material-ui/core/Select'
import CircularProgress from '@material-ui/core/CircularProgress'
import WeatherIcon from '@material-ui/icons/WbSunny'
import TemperatureIcon from '@material-ui/icons/ShowChart'

type PlaceType = {name: string, id: number}

const WeatherPage: React.FC = () => {
  const [placeIndex, setPlaceIndex] = useState(-1)
  const [weather, setWeather] = useState("")
  const [temperature, setTemperature] = useState(0)
  const [loading, setLoading] = useState(false)

  const Places:PlaceType[] = [
    {name: '札幌', id: 2128295}, {name: '東京', id: 1850147},
    {name: '大阪', id: 1853909}, {name: '沖縄', id: 1894616}]
  const OpenWeatherMapKey = "＊＊ ここに取得したAPIキーを書いて下さい ＊＊"

  const selectPlace = (index: number) => {
    setLoading(true)
    setPlaceIndex(index)
    getWeather(Places[index].id)
  }

  const getWeather = async (id: number) => {
    const delay = (mSec: number) => new Promise((resolve) => setTimeout(resolve, mSec))

    const response = await fetch(
      `http://api.openweathermap.org/data/2.5/weather?appid=${OpenWeatherMapKey}&id=${id}&\
lang=ja&units=metric`)
    const json = await response.json()
    await delay(700)
    setWeather(json.weather[0].description)
    setTemperature(json.main.temp)
    setLoading(false)
  }

  const placeName = placeIndex === -1 ? "" : Places[placeIndex].name
  return (
    <Card style={{margin: 30}}>
      <CardHeader title={<Title placeName={placeName} />} />
      <CardContent style={{position: 'relative'}}>
        {loading ? <CircularProgress style={{position: "absolute", top:40, left: 100}} /> : null}
        <WeaterInfomation weather={weather} temperature={temperature} />
      </CardContent>
      <CardActions>
        <PlaceSelector places={Places} placeIndex={placeIndex} actionSelect={(ix) => selectPlace(ix)} />
      </CardActions>
    </Card>
  )
}

type TitleProps = {
  placeName: string
}
const Title: React.FC<TitleProps> = (props) => (
  <h1>{props.placeName ? props.placeName + 'の天気' : '天気情報'}</h1>
)

type WeaterInfomationProps = {
  weather: string
  temperature: number
}
const WeaterInfomation: React.FC<WeaterInfomationProps> = ({weather, temperature}) => (
  <List>
    <ListItem>
      <ListItemIcon><WeatherIcon/></ListItemIcon>
      <ListItemText primary={weather} />
    </ListItem>
    <ListItem>
      <ListItemIcon><TemperatureIcon/></ListItemIcon>
      <ListItemText primary={temperature ? `${temperature} ℃` : ''} />
    </ListItem>
  </List>
)

ttype PlaceSelectorProps = {
  places: PlaceType[]
  placeIndex: number
  actionSelect: (index: number) => void
}
const PlaceSelector: React.FC<PlaceSelectorProps> = ({places, placeIndex, actionSelect}) => (
  <FormControl style={{width: 200}}>
    <InputLabel>場所を選択</InputLabel>
    <Select value={placeIndex} onChange={
      (event) => actionSelect(Number(event.target.value))}>
      {places.map((place, ix) => <MenuItem key={ix} value={ix}>{place.name}</MenuItem>)}
    </Select>
  </FormControl>
)


ReactDOM.render(<WeatherPage />, document.getElementById('root'))
~~~

## 付録: GraphQLサンプル

* src/index.tsx

~~~js
import React from 'react'
import ReactDOM from 'react-dom'
import ApolloClient from "apollo-boost"
import { ApolloProvider } from 'react-apollo'
import gql from "graphql-tag"
import { useQuery } from '@apollo/react-hooks'

const client = new ApolloClient({
  uri: 'https://api.github.com/graphql',
  request: operation => {
    operation.setContext({
      headers: {
        authorization: `Bearer ${
          process.env.REACT_APP_GITHUB_PERSONAL_ACCESS_TOKEN
        }`
      }
    });
  }
})

type ReositoryNode = {
  id: string
  name: string
  stargazers: {totalCount: number}
}
type GitHubUserRepostories = {
  user: {
    repositories: {nodes: ReositoryNode[]}
  }
}
// ↓ ②
const query = gql`
  {
    user(login: "＊＊ あなたのGitHubアカウント ＊＊") {
      repositories(first: 100, isFork: false, orderBy: {field: NAME, direction: ASC}) {
        nodes {
          id
          name
          stargazers {
            totalCount
          }
        }
      }
    }
  }`

const App = () => {
    const { loading, error, data } = useQuery<GitHubUserRepostories>(query)

    if (loading) return <p>Loading...</p>
    if (error) return <p>Error!</p>

    const repositories = data ? data.user.repositories.nodes : []
    return (
      <ul>
        {repositories.map(repo => (
          <li key={repo.id}>
            {repo.name} ★ {repo.stargazers.totalCount}
          </li>
        ))}
      </ul>
    )
}


ReactDOM.render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>,
  document.getElementById('root')
)
~~~

