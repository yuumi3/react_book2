# コンポーネントの応用

## JyankenのTabをExpansion Panelに変更

~~~js
import React, { useState, useMemo } from 'react'
import ReactDOM from 'react-dom'
import Button from '@material-ui/core/Button'
import Paper from '@material-ui/core/Paper'
import ExpansionPanel from '@material-ui/core/ExpansionPanel'
import ExpansionPanelDetails from '@material-ui/core/ExpansionPanelDetails'
import ExpansionPanelSummary from '@material-ui/core/ExpansionPanelSummary'
import Typography from '@material-ui/core/Typography'
import Table from '@material-ui/core/Table'
import TableBody from '@material-ui/core/TableBody'
import TableCell from '@material-ui/core/TableCell'
import TableHead from '@material-ui/core/TableHead'
import TableRow from '@material-ui/core/TableRow'
import Jyanken, { Statuses, Score, Te, Judgment } from './Jyanken'

const JyankeGamePage: React.FC = () => {
  const [scores, setScores] = useState<Score[]>([])
  const [status, setStatus] = useState<Statuses>({draw: 0, win: 0, lose: 0})
  const [panelIndex, setPanelIndex] = useState(0)
  const jyanken = useMemo(() => new Jyanken(), [])

  const panelChange = (ix: number)=> (event: React.ChangeEvent<{}>, newExpanded: boolean) => {
    setPanelIndex(ix)
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

  const panelStyle = {color: '#fff', backgroundColor: '#01bcd4'}
  return (
    <div style={{marginLeft: 30}}>
      <Header>じゃんけん ポン！</Header>
      <JyankenBox actionPon={(te) => pon(te)} />
      <Paper style={{width: 500}}>
        <ExpansionPanel expanded={panelIndex === 0} onChange={panelChange(0)}>
          <ExpansionPanelSummary aria-controls="panel1-content" id="panel1-header" style={panelStyle}>
          <Typography>対戦結果</Typography>
          </ExpansionPanelSummary>
          <ExpansionPanelDetails>
          <ScoreList scores={scores} />
          </ExpansionPanelDetails>
        </ExpansionPanel>
        <ExpansionPanel expanded={panelIndex === 1} onChange={panelChange(1)}>
          <ExpansionPanelSummary aria-controls="panel2-content" id="panel2-header" style={panelStyle}>
          <Typography>対戦成績</Typography>
          </ExpansionPanelSummary>
          <ExpansionPanelDetails>
          <StatusBox status={status} />
          </ExpansionPanelDetails>
        </ExpansionPanel>
      </Paper>
    </div>
  )
}

・・・ 以下は同じ・・・
~~~

## 天気アプリに湿度表示を追加


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
import OpacityIcon from '@material-ui/icons/Opacity'

type PlaceType = {name: string, id: number}

const WeatherPage: React.FC = () => {
  const [placeIndex, setPlaceIndex] = useState(-1)
  const [weather, setWeather] = useState("")
  const [temperature, setTemperature] = useState(0)
  const [humidity, setHumidity] = useState(0)
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

    try {
      const response = await fetch(
        `http://api.openweathermap.org/data/2.5/weather?appid=${OpenWeatherMapKey}&id=${id}&lang=ja&units=metric`)
      const json = await response.json()
      await delay(700)
      console.log(json)
      setWeather(json.weather[0].description)
      setTemperature(json.main.temp)
      setHumidity(json.main.humidity)
      setLoading(false)
    } catch (error) {
      setLoading(false)
      console.log('** error **', error)
    }
  }

  const placeName = placeIndex === -1 ? "" : Places[placeIndex].name
  return (
    <Card style={{margin: 30}}>
      <CardHeader title={<Title placeName={placeName} />} />
      <CardContent style={{position: 'relative'}}>
        {loading ? <CircularProgress style={{position: "absolute", top:40, left: 100}} /> : null}
        <WeaterInfomation weather={weather} temperature={temperature} humidity={humidity} />
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
  humidity: number
}
const WeaterInfomation: React.FC<WeaterInfomationProps> = ({weather, temperature, humidity}) => (
  <List>
    <ListItem>
      <ListItemIcon><WeatherIcon/></ListItemIcon>
      <ListItemText primary={weather} />
    </ListItem>
    <ListItem>
      <ListItemIcon><TemperatureIcon/></ListItemIcon>
      <ListItemText primary={temperature ? `${temperature} ℃` : ''} />
    </ListItem>
    <ListItem>
      <ListItemIcon><OpacityIcon/></ListItemIcon>
      <ListItemText primary={humidity ? `${humidity} ％` : ''} />
    </ListItem>
  </List>
)

・・・ 以下は同じ・・・
~~~



