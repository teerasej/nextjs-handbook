
# Static Generation with Data from API

> จะทำส่วนนี้ได้ [ต้องทำ workshop นี้ก่อน](../simple-static-generation/README.md)

> ในที่นี้เราจะใช้ข้อมูลจาก https://covid19.ddc.moph.go.th/api/Cases/today-cases-all

1. สร้างไฟล์ `pages/covid-result.tsx` และเขียนคำสั่ง import ที่จำเป็น

```ts
import { GetStaticProps, NextPage, InferGetStaticPropsType } from 'next'
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
export const getStaticProps: GetStaticProps = async (context) => {

    const response = await fetch('https://covid19.ddc.moph.go.th/api/Cases/today-cases-all', {
        headers: {
            'Content-Type': 'application/json'
        },
        method: 'GET'
    })

    const result: CovidResultInfo = await response.json()

    return {
        props: {
            result
        }
    }
}
```


- สังเกตว่ามีการกำหนด type ที่ได้จากการแปลง json เป็น type ที่เราต้องการเรียกใช้ข้อมูล
- ค่า `props` นี้จะถูกส่งเข้าไปใน page 


3. เขียนสร้าง Page component เป็นแบบด้านล่างโดยมีการเรียกใช้ props

```ts
const CovidResult: NextPage = ({ result }: InferGetStaticPropsType<typeof getStaticProps>) => {

    const resultObject = result as CovidResultInfo

    return (
        <div>
            <h1>Update at {resultObject[0].txn_date}</h1>
            <p>New case: {resultObject[0].new_case}</p>
        </div>
    )
}

export default CovidResult
```
- สังเกตว่ามีการแปลง type ของ `result` ที่ได้มาจาก props เป็น type `CovidResultInfo`
  
4. บันทึกผลและทดสอบการทำงาน