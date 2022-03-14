

# Client-side rendering


> ในที่นี้เราจะใช้ข้อมูลจาก https://covid19.ddc.moph.go.th/api/Cases/today-cases-all

1. สร้างไฟล์ `pages/covid/recover.tsx` และเขียนคำสั่ง import ที่จำเป็น

```ts
import React, { useEffect, useState } from 'react'
import { NextPage } from 'next/types'
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

3. สร้าง NextPage component ชื่อ `RecoverPage` 

```tsx
const RecoverPage: NextPage = () => {

    if(!covidResultInfo) return <h1>loading...</h1>
    
    return (
        <div>
        </div>
    )
}

export default RecoverPage;
```

4. ประกาศ `useState` เพื่อเก็บข้อมูลจาก API 

```ts
const [covidResultInfo, setCovidResultInfo] = useState<CovidResultInfo>()
```

5. ประกาศ `useEffect` เพื่อรันโค้ดฝั่ง client ใน component

```ts
useEffect(() => {
        const loadData = async () => {
            const response = await fetch('https://covid19.ddc.moph.go.th/api/Cases/today-cases-all', {
                headers: {
                    'Content-Type': 'application/json'
                },
                method: 'GET'
            })

            const result:CovidResultInfo = await response.json()
            setCovidResultInfo(result)
        }

        loadData()
    }, [])
```

6. เรียกใช้ค่าจาก state hook ใน JSX
  
```tsx
if(!covidResultInfo) return <h1>loading...</h1>
    
    return (
        <div>
            <h1>Update at {covidResultInfo[0].txn_date}</h1>
            <p>Recover: {covidResultInfo[0].total_recovered}</p>
        </div>
    )
```

7. บันทึกผลและทดสอบการทำงาน ที่ URL http://localhost:3000/covid/recover 

## ไฟล์สมบูรณ์

```tsx

import React, { useEffect, useState } from 'react'
import { NextPage } from 'next/types'

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

const RecoverPage: NextPage = () => {

    const [covidResultInfo, setCovidResultInfo] = useState<CovidResultInfo>()

    useEffect(() => {
        const loadData = async () => {
            const response = await fetch('https://covid19.ddc.moph.go.th/api/Cases/today-cases-all', {
                headers: {
                    'Content-Type': 'application/json'
                },
                method: 'GET'
            })

            const result:CovidResultInfo = await response.json()
            setCovidResultInfo(result)
        }

        loadData()
    }, [])

    if(!covidResultInfo) return <h1>loading...</h1>
    
    return (
        <div>
            <h1>Update at {covidResultInfo[0].txn_date}</h1>
            <p>Recover: {covidResultInfo[0].total_recovered}</p>
        </div>
    )
}

export default RecoverPage;
```