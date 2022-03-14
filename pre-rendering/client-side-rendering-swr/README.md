

# Client-side rendering with SWR


> ในที่นี้เราจะใช้ข้อมูลจาก https://covid19.ddc.moph.go.th/api/Cases/today-cases-all

1. สร้างไฟล์ `pages/covid/exclude-abroad.tsx` และเขียนคำสั่ง import ที่จำเป็น

```ts
import React from 'react'
import { NextPage } from 'next/types'
import useSWR from 'swr'

```

2. ประกาศ type เพื่อใช้ในการอ้างอิง[ข้อมูลที่จะได้จาก Web API](https://covid19.ddc.moph.go.th/api/Cases/today-cases-all) 

```ts
type CovidResultInfo = [{
    txn_date: string,
    new_case: Number,
    total_case: Number,
    new_case_excludeabroad: Number,
    total_case_excludeabroad: Number,
    new_death: Number,
    total_death: Number,
    new_recovered: Number,
    total_recovered: Number,
    update_date: string
}]
```

3. สร้าง NextPage component ชื่อ `ExcludeAbroadPage` 

```tsx
const ExcludeAbroadPage: NextPage = () => {
    
    return (
        <div>
        </div>
    )
}

export default ExcludeAbroadPage;
```

4. สร้างการทำงานของ `fetcher` function เพื่อใช้งานกับ `useSWR` hook

```ts
const fetcher = (...args:any) => fetch(args).then((res) => res.json())
```

5. เรียกใช้งาน useSWR hook โดยกำหนด fetcher function 

```ts
const { data, error } = useSWR('https://covid19.ddc.moph.go.th/api/Cases/today-cases-all', fetcher)

const covidResultInfo = data as CovidResultInfo
```
- สังเกตว่าเราแปลง `data` ให้เป็น type `CovidResultInfo`



6. ใช้ค่าที่ได้จาก useSWR hook ในการ render แบบต่างๆ 
  
```tsx
if (error) return <div>Failed to load</div>
if(!covidResultInfo) return <h1>loading...</h1>
    
    return (
        <div>
            <h1>Update at {covidResultInfo[0].txn_date}</h1>
            <p>New case exclude abroad: {covidResultInfo[0].new_case_excludeabroad}</p>
            <p>Total case exclude abroad: {covidResultInfo[0].total_case_excludeabroad}</p>
        </div>
    )
```

7. บันทึกผลและทดสอบการทำงาน ที่ URL http://localhost:3000/covid/exclude-abroad 

## ไฟล์สมบูรณ์

```tsx


import React from 'react'
import { NextPage } from 'next/types'
import useSWR from 'swr'

const fetcher = (...args:any) => fetch(args).then((res) => res.json())

type CovidResultInfo = [{
    txn_date: string,
    new_case: Number,
    total_case: Number,
    new_case_excludeabroad: Number,
    total_case_excludeabroad: Number,
    new_death: Number,
    total_death: Number,
    new_recovered: Number,
    total_recovered: Number,
    update_date: string
}]

const ExcludeAbroadPage: NextPage = () => {


    const { data, error } = useSWR('https://covid19.ddc.moph.go.th/api/Cases/today-cases-all', fetcher)

    const covidResultInfo = data as CovidResultInfo

    if (error) return <div>Failed to load</div>
    if(!covidResultInfo) return <h1>loading...</h1>
    
    return (
        <div>
            <h1>Update at {covidResultInfo[0].txn_date}</h1>
            <p>New case exclude abroad: {covidResultInfo[0].new_case_excludeabroad}</p>
            <p>Total case exclude abroad: {covidResultInfo[0].total_case_excludeabroad}</p>
        </div>
    )
}

export default ExcludeAbroadPage;
```