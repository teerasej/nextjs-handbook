
# Accessing API

## 1. สร้าง function สำหรับ submit ข้อมูล

เปิดไฟล์ **pages/signin.tsx** และเพิ่มการทำงานของ function สำหรับรับข้อมูลจาก Form

```tsx
import { NextPage } from "next";
import { FormEvent } from "react";

// สร้าง Type ที่เป็นตัวแทนของ field ใน form component
type LoginFormElements = {
    username: HTMLInputElement,
    password: HTMLInputElement
} & HTMLCollection

const SignInPage: NextPage = () => {

    // สร้าง function สำหรับ FormEvent
    const goSignIn = async (event:FormEvent<HTMLFormElement>) => {

        event.preventDefault();

        //เข้าถึง HTMLFormElement
        const loginForm =  (event.currentTarget as HTMLFormElement).elements as LoginFormElements

        // ส่ง request ไปที่ API และรับ Response ตอบกลับ
        const response = await fetch('/api/auth', {
          body: JSON.stringify({
            username: loginForm.username.value,
            password: loginForm.password.value
          }),
          headers: {
            'Content-Type': 'application/json'
          },
          method: 'POST'
        })

      }

      // นำ function มาใช้กับ form
      return (
        <form onSubmit={goSignIn}>
          <label data-for="username">username:</label>
          <input type="text" id="username" name="username" />
          <label data-for="password">password:</label>
          <input type="password" id="password" name="password" />
          <button type="submit">Sign in</button>
        </form>
      )
}

export default SignInPage
```

## 2. เข้าถึง JSON ใน Request

เปิดไฟล์ **pages/api/auth.ts** และทำการแก้ไขโค้ดตามด้านล่าง

```ts

import type { NextApiRequest, NextApiResponse} from "next"

// สร้าง type ที่รวมคุณสมบัติเดิมของ NextApiRequest กับข้อมูล json ที่เราต้องการแก้ไข
type AuthenAPIRequest = {
    body: {
        username?:string,
        password?:string
    }
} & NextApiRequest


export default function handler( 
    // ใช้ AuthenAPIRequest แทน NextApiRequest
    req: AuthenAPIRequest,
    res: NextApiResponse
) {
    // เข้าถึงข้อมูลที่มากับ request ในส่วน body
    const { username, password } = req.body

    // ใช้ response ส่งข้อมูลกลับไปที่ client
    res.status(200).json({ 
        result: true, 
        message: `Hello ${username}`
    })
}
```

## 3. การรับข้อมูล JSON จาก API

- เปิดไฟล์ **pages/signin.tsx** และทำการเพิ่มการทำงานของระบบด้วยโค้ดด้านล่าง
- ทดสอบการทำงาน

```tsx
import { NextPage } from "next";
import { FormEvent } from "react";

type LoginFormElements = {
    username: HTMLInputElement,
    password: HTMLInputElement
} & HTMLCollection

// สร้าง Type ที่เป็นตัวแทนของ JSON ที่ได้กลับมาจาก API
type AuthResult = {
    message:string
}

const SignInPage: NextPage = () => {

    
    const goSignIn = async (event:FormEvent<HTMLFormElement>) => {

        event.preventDefault();

        const loginForm =  (event.currentTarget as HTMLFormElement).elements as LoginFormElements

        const response = await fetch('/api/auth', {
          body: JSON.stringify({
            username: loginForm.username.value,
            password: loginForm.password.value
          }),
          headers: {
            'Content-Type': 'application/json'
          },
          method: 'POST'
        })

        // เข้าถึงค่า Response ในรูปแบบ json
        const result:AuthResult = await response.json()
        console.log(result.message)
      }

      return (
        <form onSubmit={goSignIn}>
          <label data-for="username">username:</label>
          <input type="text" id="username" name="username" />
          <label data-for="password">password:</label>
          <input type="password" id="password" name="password" />
          <button type="submit">Sign in</button>
        </form>
      )
}

export default SignInPage
```