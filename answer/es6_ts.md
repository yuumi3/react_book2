# ES6とTypeScript

## JavaScript(ES5)をTypeScript(ES6)に書き換える

~~~js
type Person = {
  name: string
  age: number
}

let people:Person[] = []

const addPerson = (name: string, age: number = 18) => {
  const person: Person = {name: name, age: age}
  people.push(person)
}

addPerson('Yamada', 21)
addPerson('Kawada', 30)
addPerson('Umida')

people.forEach(p => {console.log(`name: ${p.name}, age: ${p.age}`)})
~~~

