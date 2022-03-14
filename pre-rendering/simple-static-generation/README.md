
# Simple Static Generation 

> จะทำส่วนนี้ได้ [ต้องทำ workshop นี้ก่อน](../simple-page-rendering/README.md)

1. ประกาศ `getStaticProps` function ตามด้านล่าง

```ts
import { GetStaticProps, NextPage, InferGetStaticPropsType } from 'next'

export const getStaticProps: GetStaticProps = async (context) => {
  
  return {
    props: {
      username: 'tody',
      members:  ['Tony', 'Peter']
    }
  }
}
```
- สังเกตว่ามีการกำหนด type ของ function เป็น `GetStaticProps` และการทำงานเป็นแบบ `async`
- ค่า `props` นี้จะถูกส่งเข้าไปใน page 

2. ประกาศ type เพื่อใช้ในการอ้างอิง

```ts
type Team = {
  username:string,
  members:string[]
}
```
- สังเกตว่าชื่อ property และ data type สอดคล้องกับข้อมูลที่ถูก return ออกมาจาก `GetStaticProps` 

3. แก้ไข Page component เป็นแบบด้านล่างโดยมีการเรียกใช้ props

```ts
import { GetStaticProps, NextPage, InferGetStaticPropsType } from 'next'

const StaticGenerationPost: NextPage = ({ username, members }: InferGetStaticPropsType<typeof getStaticProps>) => {

  console.log(username)
  console.log(members?.[0])

  return (
    <div>1. {username} with {members?.length} members</div>
  )
}
```
- สังเกตว่ามีการกำหนด data type ให้กับค่า props ด้วย `{ username, members }: InferGetStaticPropsType<typeof getStaticProps>`
  
4. บันทึกผลและทดสอบการทำงาน