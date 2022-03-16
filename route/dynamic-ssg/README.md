
# Dynamic Route on Static Generation Page

> จะทำส่วนนี้ได้ [ต้องทำ workshop นี้ก่อน](../common-route/README.md)

## 1. ดูและสร้างโครงสร้างของข้อมูล

1. สังเกตว่าเรามีไฟล์ **data/mock_data.json** อยู่
2. สร้างไฟล์ **types/user-array.ts** เพื่อเป็นตัวแทนของข้อมูล JSON ด้านบน

```ts
export type UserArray = [User]

export type User = {
    id:Number,
    firstName:string,
    lastName:string,
    email:string,
    gender:string
}

```

## 2. สร้าง menu แสดงรายชื่อ user 

สร้างไฟล์ **pages/users-static/index.tsx**

```tsx
import { GetStaticProps, InferGetStaticPropsType, NextPage } from "next";
import Link from "next/link";
import { UserArray } from "../../types/user-array";


// ใช้ GetStaticProps ในการ load และ genenrate page
export const getStaticProps: GetStaticProps = async () => {

    const results: UserArray = require('../../data/mock_data.json')


    return {
        props: {
            results
        }
    }
}

const UserListPage: NextPage = ({ results }: InferGetStaticPropsType<typeof getStaticProps>) => {

    return (
        <div>
            <h1>Users</h1>
            {
                (results as UserArray).map(user => {
                    return (
                        <Link href={`users-static/${user.id}`} key={user.id.toString()}>
                            <p>{user.firstName} {user.lastName}</p>
                        </Link>
                    )
                })
            }
        </div>
    )
}

export default UserListPage
```

## 3. สร้าง dynamic route page 

สร้างไฟล์​ **pages/users-static/[id].tsx**

- ในที่นี้เราจะใช้ **GetStaticPaths** สร้าง map ของค่าที่ส่งมาจาก route url ว่ามีหรือไม่มีค่านี้
- เสร็จแล้วทดสอบการทำงาน

```tsx
import { GetStaticPaths, GetStaticProps, GetStaticPropsContext, InferGetStaticPropsType, NextPage } from "next"
import { ParsedUrlQuery } from "querystring"

// import type user ที่สร้างไว้
import type { UserArray, User } from '../../types/user-array'


// สร้าง interface สำหรับดึงค่า id ออกจาก context
interface IParams extends ParsedUrlQuery {
    id:string
}

// สร้าง path map เพื่อใช้ในการเช็คว่ามีข้อมูลตรงกับที่ route url params ส่งมาที่ page นี้ไหม
export const getStaticPaths: GetStaticPaths = async () => {
    const users:UserArray = require('../../data/mock_data.json')

    const generatedPaths = users.map(user => ({
        params: { id: user.id.toString() }
    }))

    return {
        paths: generatedPaths
    }

}


export const getStaticProps:GetStaticProps = async (context:GetStaticPropsContext) => {

    // ดึง url parameter ที่ชื่อ id (ตรงกับการตั้งชื่อไฟล์) ออกมาเพื่อใช้งาน
    const { id } = context.params as IParams

    const users: UserArray = require('../../data/mock_data.json')

    // ค้นหาข้อมูลที่ต้องการเพื่อใช้ในการ render page โดยอิงจาก id ที่ได้
    const filteredUsers = users.filter((user) => user.id.toString() == id)

    return {
        props: {
            userProfile: filteredUsers[0]
        }
    }
}

const UserProfilePage: NextPage = ({ userProfile }: InferGetStaticPropsType<typeof getStaticProps>) => {

    const user = userProfile as User

    return (
        <div>
            <h1>{user.firstName} {user.lastName}</h1>
        </div>
    )
}

export default UserProfilePage
```