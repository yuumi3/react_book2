# Redux

## ジャンケンアプリ

### インストール

~~~shell
npm install @reduxjs/toolkit react-redux redux-logger @types/react-redux @types/redux-logger
~~~

※ プロンプトは省略しました

### コード

* src/jyankenSlice.ts  (src¥jyankenSlice.ts)

~~~js
import { createSlice, PayloadAction } from '@reduxjs/toolkit'
import { combineReducers } from '@reduxjs/toolkit'

export enum Te { Guu, Choki, Paa }
export enum Judgment {Draw, Win, Lose}
export interface Score {human: Te, computer: Te, created_at: Date, judgment: Judgment}
export interface Statuses {draw: number, win: number, lose: number}

export type JyankenState = {
  scores: Score[]
  statuses: Statuses
}
type  JyanenPonPlayload = Te | {human:Te, computer:Te}

const jyankenSlice = createSlice({
  name: "jyanken",
  initialState: {scores: [], statuses: {draw: 0, win: 0, lose: 0}} as JyankenState,
  reducers: {
    pon(state: JyankenState, action: PayloadAction<JyanenPonPlayload>) : void {
      let human, computer

      if (typeof action.payload === "number") {
        human = action.payload
        computer = Math.floor(Math.random() * 3)
      } else {
        human = action.payload.human
        computer = action.payload.computer
      }
      const judgment = (computer - human + 3) % 3
      state.scores.unshift({human: human, computer: computer, created_at: new Date(), judgment: judgment})
      state.statuses = calcStatus(state.scores)
    }
  }
})

export const { pon } = jyankenSlice.actions
export const jyankenReducer = jyankenSlice.reducer

export const rootReducer = combineReducers({
  jyanken: jyankenReducer
})
export type RootState = ReturnType<typeof rootReducer>


const calcStatus = (scores: Score[]):Statuses => {
  const countScore = (judge: Judgment): number => scores.filter(e => e.judgment === judge).length
  return {draw: countScore(Judgment.Draw), win: countScore(Judgment.Win), lose: countScore(Judgment.Lose)}
}
~~~

* src/App.tsx  (src¥App.tsx)

~~~js
import React from 'react'
import { Switch, Route,  Redirect, Link, useLocation } from 'react-router-dom'
import { useDispatch, useSelector } from  'react-redux'
import Button from '@material-ui/core/Button'
import Paper from '@material-ui/core/Paper'
import Table from '@material-ui/core/Table'
import TableBody from '@material-ui/core/TableBody'
import TableCell from '@material-ui/core/TableCell'
import TableHead from '@material-ui/core/TableHead'
import TableRow from '@material-ui/core/TableRow'
import { Judgment, pon, Te, Score, RootState } from './jyankenSlice'

export const App: React.FC = () => {
  let location = useLocation()
  const tabStyle = {width: 200, height: 50, color: '#fff', backgroundColor: '#01bcd4'}
  const activeStyle = (path: string) => {
    const borderBottomColor = location.pathname.match(path) ? "#f00" : "#01bcd4"
    return {...tabStyle,  ...{borderBottom: `solid 2px ${borderBottomColor}`}}
  }
  return (
    <div style={{marginLeft: 30}}>
      <Header>じゃんけん ポン！</Header>
      <JyankenBox />
      <Paper style={{width: 400}}>
        <Link id="tab-scores" to="/scores" style={{textDecoration: 'none'}}><Button style={activeStyle('scores')}>対戦結果</Button></Link>
        <Link id="tab-status" to="/status" style={{textDecoration: 'none'}}><Button style={activeStyle('status')}>対戦成績</Button></Link>
        <Switch>
          <Route path="/scores" component={() => <ScoreList />}/>
          <Route path="/status" component={() => <StatusBox />}/>
          <Route exact path="/" component={() => <Redirect to="/scores" />}/>
        </Switch>
      </Paper>
    </div>
  )
}

const Header: React.FC = (props) => (<h1>{props.children}</h1>)

const judgmentStyle = (judgment: Judgment) => (
  {paddingRight: 16, color: ["#000", "#2979FF", "#FF1744"][judgment]})


const StatusBox: React.FC = () => {
  const statuses = useSelector((state:RootState) =>  state.jyanken.statuses)
  return (
    <Table>
      <TableBody>
        <TableRow>
          <TableCell variant="head">勝ち</TableCell><TableCell style={judgmentStyle(Judgment.Win)}>{statuses.win}</TableCell>
        </TableRow>
        <TableRow>
          <TableCell variant="head">負け</TableCell><TableCell style={judgmentStyle(Judgment.Lose)}>{statuses.lose}</TableCell>
        </TableRow>
        <TableRow>
          <TableCell variant="head">引き分け</TableCell><TableCell style={judgmentStyle(Judgment.Draw)}>{statuses.draw}</TableCell>
        </TableRow>
      </TableBody>
    </Table>
  )
}


const JyankenBox: React.FC = () => {
  const dispatch = useDispatch()
  const style = {marginLeft: 20}
  return (
    <div style={{marginTop: 40, marginBottom: 30, marginLeft: 50}}>
      <Button variant="contained" id="btn-guu" onClick={() => dispatch(pon(Te.Guu))} style={style}>グー</Button>
      <Button variant="contained" id="btn-choki" onClick={() => dispatch(pon(Te.Choki))} style={style}>チョキ</Button>
      <Button variant="contained" id="btn-paa" onClick={() => dispatch(pon(Te.Paa))} style={style}>パー</Button>
    </div>
  )
}

const ScoreList: React.FC = () => {
  const scores: Score[] = useSelector((state:RootState) =>  state.jyanken.scores)
  return (
    <Table>
      <TableHead>
        <TableRow>
          <TableCell>時間</TableCell><TableCell>人間</TableCell><TableCell>コンピュータ</TableCell><TableCell>結果</TableCell>
        </TableRow>
      </TableHead>
      <TableBody>
        {scores.map((score, ix) => <ScoreListItem key={ix} score={score} />)}
      </TableBody>
    </Table>
  )
}

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
~~~


* src/index.tsx  (src¥index.tsx)

~~~js
import React from 'react'
import ReactDOM from 'react-dom'
import logger from 'redux-logger'

import { App } from './App'
import { BrowserRouter as Router } from 'react-router-dom'
import { Provider } from  'react-redux'
import { rootReducer } from './jyankenSlice'
import { configureStore, getDefaultMiddleware } from '@reduxjs/toolkit'

const middlewares = [...getDefaultMiddleware({serializableCheck: false}), logger]
const store = configureStore({
    reducer: rootReducer,
    middleware: middlewares,
})


ReactDOM.render(
  <Provider store={store}>
    <Router>
      <React.StrictMode>
        <App />
      </React.StrictMode>,
    </Router>
  </Provider>,
  document.getElementById('root')
)
~~~


## weatherアプリ

API通信等の非同期処理

### インストール

~~~shell
npm install @reduxjs/toolkit react-redux redux-logger @types/react-redux @types/redux-logger
~~~

※ プロンプトは省略しました

### コード

* src/App.tsx

~~~js
import React from 'react'
import { useDispatch, useSelector } from  'react-redux'
import { combineReducers, createSlice, PayloadAction } from '@reduxjs/toolkit'
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
const Places:PlaceType[] = [
  {name: '札幌', id: 2128295}, {name: '東京', id: 1850147},
  {name: '大阪', id: 1853909}, {name: '沖縄', id: 1894616}]
const OpenWeatherMapKey = "＊＊ ここに取得したAPIキーを書いて下さい ＊＊"


export const App: React.FC = () => {
  const loading = useSelector((state:RootState) =>  state.weather.loading)
  const placeIndex = useSelector((state:RootState) =>  state.weather.placeIndex)

  const placeName = placeIndex === -1 ? "" : Places[placeIndex].name
  return (
    <Card style={{margin: 30}}>
      <CardHeader title={<Title placeName={placeName} />} />
      <CardContent style={{position: 'relative'}}>
        {loading ? <CircularProgress style={{position: "absolute", top:40, left: 100}} /> : null}
        <WeaterInfomation />
      </CardContent>
      <CardActions>
        <PlaceSelector />
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

const WeaterInfomation: React.FC = () => {
  const weather = useSelector((state:RootState) =>  state.weather)
  return (
    <List>
      <ListItem>
        <ListItemIcon><WeatherIcon/></ListItemIcon>
        <ListItemText primary={weather.weather} />
      </ListItem>
      <ListItem>
        <ListItemIcon><TemperatureIcon/></ListItemIcon>
        <ListItemText primary={weather.temperature ? `${weather.temperature} ℃` : ''} />
      </ListItem>
    </List>
  )
}

const PlaceSelector: React.FC = () => {
  const dispatch = useDispatch()
  const placeIndex = useSelector((state:RootState) =>  state.weather.placeIndex)
  return (
    <FormControl style={{width: 200}}>
      <InputLabel>場所を選択</InputLabel>
      <Select value={placeIndex} onChange={(event) => dispatch(getWeather(Number(event.target.value)))}>
        {Places.map((place, ix) => <MenuItem key={ix} value={ix}>{place.name}</MenuItem>)}
      </Select>
    </FormControl>
  )
}

// ==============================

export type WeatherState = {
  placeIndex: number
  weather: string
  temperature: number
  loading: boolean
}

const weatherSlice = createSlice({
  name: "jyanken",
  initialState: {placeIndex: -1, weather: "", temperature: 0, loading: false} as WeatherState,
  reducers: {
    selectPlace(state: WeatherState, action: PayloadAction<number>): void {
      state.placeIndex = action.payload
      state.weather = ""
      state.temperature = 0
      state.loading = true
    },
    getWeatherSucceed(state: WeatherState, action: PayloadAction<{weather: string, temperature:number}>):void {
      state.weather = action.payload.weather
      state.temperature = action.payload.temperature
      state.loading = false
    },
    getWeatherFaild(state: WeatherState, action: PayloadAction): void {
      state.weather = "情報取得に失敗しました"
      state.temperature = 0
      state.loading = false
    }
  }
})

const { selectPlace, getWeatherSucceed, getWeatherFaild } =  weatherSlice.actions
const weatherSReducer = weatherSlice.reducer

export const getWeather = (index: number) => {
  const delay = (mSec: number) => new Promise((resolve) => setTimeout(resolve, mSec))

  return async (dispatch: any): Promise<void> => {
    dispatch(selectPlace(index))
    const id = Places[index].id
    try {
      const response = await fetch(
        `http://api.openweathermap.org/data/2.5/weather?appid=${OpenWeatherMapKey}&id=${id}&lang=ja&units=metric`)
      const json = await response.json()
      await delay(700)
      dispatch(getWeatherSucceed({weather: json.weather[0].description, temperature: json.main.temp}))
    } catch (error) {
      dispatch(getWeatherFaild())
      console.log('** error **', error)
    }
  }
}


export const rootReducer = combineReducers({
  weather: weatherSReducer
})

type RootState = ReturnType<typeof rootReducer>
~~~

* src/index.tsx

~~~js
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider } from  'react-redux'
import { configureStore, getDefaultMiddleware } from '@reduxjs/toolkit'
import logger from 'redux-logger'
import { App, rootReducer } from './App'

const middlewares = [...getDefaultMiddleware({serializableCheck: false}), logger]
const store = configureStore({
    reducer: rootReducer,
    middleware: middlewares,
})

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
~~~
