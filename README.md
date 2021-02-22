# Svelte date picker

A modern date picker for Svelte.js framework

## Usage

```js
import { Picker, Calendar } from "svelte-date-picker";

let selected;
// simple form
<Picker>
 <Calendar />
</Picker>;
```

```js
import {Picker, Calendar} from 'svelte-date-picker'

let selected
let initial = new Date(selected || "2020-10-10")

<Picker  >
 <Calendar initial={initial} on:action={(date) => {selected = date}} />
</Picker>

```

## Props

### Picker

| Prop     | Type   | Default      | Usage                                            |
| -------- | ------ | ------------ | ------------------------------------------------ |
| format   | String | "dd/MM/yyyy" | used in picker input to format the selected date |
| selected | String | Date         | used in picker input to format the selected date |
| locale   | String | "us"         | used in picker input to format the selected date |

| slot variable | Type       | Default | Usage                                      |
| ------------- | ---------- | ------- | ------------------------------------------ |
| close         | () => void |         | a function use to close the calendar popup |

### Calendar

| Prop             | Type                 | Default   | Usage                                       |
| ---------------- | -------------------- | --------- | ------------------------------------------- |
| initial          | Date                 | today     | used for the starting point                 |
| selected         | Date                 | undefined | specify a selected date                     |
| disablePredicate | (date:Date)=>boolean | ()=>false | used to check if the date is not selectable |
| action           | (date:Date)=>void    | ()=>{}    | callback called when the date is selected   |
