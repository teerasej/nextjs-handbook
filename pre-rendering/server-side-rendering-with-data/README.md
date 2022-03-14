

# Server-side Rendering with Data from API


> ในที่นี้เราจะใช้ข้อมูลจาก https://covid19.ddc.moph.go.th/api/Cases/today-cases-all

1. สร้างไฟล์ `pages/covid/result.tsx` และเขียนคำสั่ง import ที่จำเป็น

```ts
import { NextPage, GetServerSideProps, InferGetServerSidePropsType } from 'next'
import React from 'react'
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

3. ประกาศ `getStaticProps` function ตามด้านล่าง

```ts
export const getServerSideProps: GetServerSideProps = async (context) => {

    const response = await fetch('https://covid19.ddc.moph.go.th/api/Cases/today-cases-all', {
        headers: {
            'Content-Type': 'application/json'
        },
        method: 'GET'
    })

    const result = await response.json()


    return {
        props: {
            result
        }
    }
}
```
- สังเกตว่าชื่อของ function คือ `getServerSideProps` และ type ของ function คือ `GetServerSideProps`
- สังเกตว่ามีการกำหนด type ที่ได้จากการแปลง json เป็น type ที่เราต้องการเรียกใช้ข้อมูล
- ค่า `props` นี้จะถูกส่งเข้าไปใน page 


3. เขียนสร้าง Page component เป็นแบบด้านล่างโดยมีการเรียกใช้ props

```ts
const CovidResultServer: NextPage = ({ result }: InferGetServerSidePropsType<typeof getServerSideProps>) => {

    const resultObject = result as CovidResultInfo

    return (
        <div>
            <h1>Update at {resultObject[0].txn_date}</h1>
            <p>New case: {resultObject[0].new_case}</p>
            <p>Total case: {resultObject[0].total_case}</p>
        </div>
    )
}

export default CovidResultServer
```
- สังเกตว่ามีการกำหนด type ของ props ที่ส่งเข้ามาใน function component เป็น `InferGetServerSidePropsType<typeof getServerSideProps>`
- สังเกตว่ามีการแปลง type ของ `result` ที่ได้มาจาก props เป็น type `CovidResultInfo`
  
4. บันทึกผลและทดสอบการทำงาน ที่ URL http://localhost:3000/covid/result 