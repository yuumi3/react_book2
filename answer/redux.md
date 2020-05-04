# Redux

## Jyankenにクリアボタンを追加

* src/jyankenSlice.ts

~~~js
import { createSlice, PayloadAction } from '@reduxjs/toolkit'
import { stat } from 'fs'

export enum Te { Guu, Choki, Paa }
export enum Judgment {Draw, Win, Lose}
export interface Score {human: Te, computer: Te, created_at: Date, judgment: Judgment}
export interface Statuses {draw: number, win: number, lose: number}

export type JyankenState = {
  scores: Score[]
  statuses: Statuses
}
type  JyanenPonPlayload = Te | {human:Te, computer:Te}
const initialState: JyankenState = {scores: [], statuses: {draw: 0, win: 0, lose: 0}}

const jyankenSlice = createSlice({
  name: "jyanken",
  initialState,
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
    },
    clear(state: JyankenState) : void {
      Object.assign(state, initialState)
    }
  }
})

export const { pon, clear } = jyankenSlice.actions
export const jyankenReducer = jyankenSlice.reducer

const calcStatus = (scores: Score[]):Statuses => {
  const countScore = (judge: Judgment): number => scores.filter(e => e.judgment === judge).length
  return {draw: countScore(Judgment.Draw), win: countScore(Judgment.Win), lose: countScore(Judgment.Lose)}
}
~~~


* src/index.tsx

~~~js
import React from 'react'
import ReactDOM from 'react-dom'
import { BrowserRouter as Router,
         Switch, Route,  Redirect, Link, useLocation } from 'react-router-dom'
import { Provider, useDispatch, useSelector } from  'react-redux'
import { configureStore, getDefaultMiddleware, combineReducers } from '@reduxjs/toolkit'
import logger from 'redux-logger'
import Button from '@material-ui/core/Button'
import Paper from '@material-ui/core/Paper'
import Table from '@material-ui/core/Table'
import TableBody from '@material-ui/core/TableBody'
import TableCell from '@material-ui/core/TableCell'
import TableHead from '@material-ui/core/TableHead'
import TableRow from '@material-ui/core/TableRow'
import { jyankenReducer, Judgment, pon, clear, Te, Score } from './jyankenSlice'
import { IconButton } from '@material-ui/core'
import DeleteIcon from '@material-ui/icons/Delete'

・・・ 省略 ・・・

const JyankenBox: React.FC = () => {
  const dispatch = useDispatch()
  const style = {marginLeft: 20}
  return (
    <div style={{marginTop: 40, marginBottom: 30, marginLeft: 50}}>
      <Button variant="contained" id="btn-guu" onClick={() => dispatch(pon(Te.Guu))} style={style}>グー</Button>
      <Button variant="contained" id="btn-choki" onClick={() => dispatch(pon(Te.Choki))} style={style}>チョキ</Button>
      <Button variant="contained" id="btn-paa" onClick={() => dispatch(pon(Te.Paa))} style={style}>パー</Button>
      <IconButton color="secondary" id="btn-clear" onClick={() => dispatch(clear())} style={style}>
        <DeleteIcon />
      </IconButton>
    </div>
  )
}

・・・  省略 ・・・
~~~
