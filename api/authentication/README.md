
# Authentication

## 1. ติดตั้ง next-auth

```
npm i next-auth
```

## 2. สร้าง environment file 

สร้างไฟล์ `.env.local` และกำหนดค่า parameter พื้นฐานลงไป

```
NEXTAUTH_SECRET=SECRET_WORD
NEXTAUTH_URL=http://localhost:3000
```

## 3. สร้าง nextauth API 

สร้างไฟล์​ **pages/api/auth/[...nextauth].ts**

```ts
import NextAuth from "next-auth"
import CredentialsProvider from 'next-auth/providers/credentials'


// For more information on each option (and a full list of options) go to
// https://next-auth.js.org/configuration/options
export default NextAuth({
  // https://next-auth.js.org/configuration/providers/oauth
  providers: [
    
    // กำหนด credential เอง
    CredentialsProvider({
      // กำนดชื่อสำหรับเรียกใช้
      name: 'credentials',
      // กำหนด pattern ของ credential 
      credentials: {
        username: { label: "Username", type: "text" },
        password: { label: "Password", type: "password" }
      },
      // ทำการ authorize ด้วยตัวเอง 
      async authorize(credentials, req) {
        
        // ...ตรงนี้ก็เช็ค authen นู่นนี่

        // ถ้าเกิด error ก็สามารถ throw ออกไปได้เลย
        if(credentials?.password == 'admin') {
          throw new Error("oops")
        }
        
        // ถ้า return null ก็ error กลับไปที่ client เหมือนกัน 
        if(credentials?.username == '1234') {
            return null
        }

        // ถ้าไม่ error ก็สามารถโยน object เพื่อเอาไปใช้ในกลไกของ next-auth ในการสร้าง token หรือ session ต่อได้
        const user = { id: 1, name: "J Smith", email: "jsmith@example.com" }
  
        if (user) {
          return user
        } else {
          
          return null
        }
      }
    })
  ],
  theme: {
    colorScheme: "light",
  },
  // callback ไว้สำหรัับ override ค่าที่จะถูกส่งกลับไปที่ client เช่น userRole ต่างๆ 
  callbacks: {
    // async session({session, token}) {
    //   return session
    // }
    // async jwt({ token, user }) {
    //   return token
    // },
  }
})
```

## 4. เรียกใช้ session เป็นเงื่อนไขในการแสดงข้อมูล

เปิดไฟล์ **pages/protected.tsx** และแก้ไขโค้ดตามด้านล่าง

```tsx
import { GetServerSideProps, InferGetServerSidePropsType, NextPage } from "next";
import { useSession } from "next-auth/react"
import { useRouter } from "next/router";

export const getServerSideProps: GetServerSideProps = async (context) => {
    return {
        props: {
            
        }
    }
}

const ProtectedPage: NextPage = ({ }: InferGetServerSidePropsType<typeof getServerSideProps>) => {

    const router = useRouter()

    // เรียกใช้ useSession และทำการเปิดไปยังหน้า signin ถ้า พบว่าไม่มี session หรือยังไม่ได้ login
    useSession({
        required: true,
        onUnauthenticated: () => {
            router.push('/signin')
        }
    })

    return (
        <div>
            <h1>Protected Page</h1>
        </div>
    )
}

export default ProtectedPage
```

## 5. เรียกใช้ signIn function ของ next-auth เพื่อทำการ authen

เปิดไฟล์ 

```tsx

import { NextPage } from "next";
import { RedirectableProviderType } from "next-auth/providers";
import { signIn } from "next-auth/react";
import Error from "next/error";
import { useRouter } from "next/router";
import { FormEvent, useState } from "react";

type LoginFormElements = {
    username: HTMLInputElement,
    password: HTMLInputElement
} & HTMLCollection

// ใช้ AuthResponse เป็น type ที่สร้างเองเพื่อเข้าถึง object ที่ next-auth response กลับมาให้เรา
type AuthResponse = {
  error: string | undefined,
  status: number,
  ok: boolean,
  url: string | null
} & RedirectableProviderType

const SignInPage: NextPage = () => {

    // กำหนด state error เพื่อใช้ในการแสดง error แทนหากการ authen ไม่สมบูรณ์
    const [error, setError] = useState<string|undefined>(undefined)

    const router = useRouter()

    const goSignIn = async (event:FormEvent<HTMLFormElement>) => {

        event.preventDefault();
        const loginForm =  (event.currentTarget as HTMLFormElement).elements as LoginFormElements

        // เรียกใช้ sign in function ของ next-auth
        // ระบุชื่อ credentials ที่ทำการ setup ไว้
        // กำหนดค่า credential ที่ต้องการส่งให้ nextauth.ts

        // เรามีการใช้ AuthResponse เป็น type ที่สร้างเองเพื่อเข้าถึง object ที่ next-auth response กลับมาให้เรา
        const res = await signIn<AuthResponse>('credentials', {
          username: loginForm.username.value,
          password: loginForm.password.value,
          // กำหนด redirect เป็น false เพื่อให้ response กลับมาที่ page เดิม 
          // สังเกตว่าไม่ได้กำหนด callbackURL
          redirect: false
        })

        // ถ้ามี error ก็ทำการกำหนด state error เพื่อ render ข้อความ error ด้านล่างของ form
        if (res?.error) {
          setError(res?.error)
        }

        // ถ้ามี url ส่งมา (ปกติเป็น redirect url ) ให้ push ไปที่ route  /protected 
        if (res?.url) {
          router.push(`/protected`)
        }

      }

      return (
         <div>
          <form onSubmit={goSignIn}>
            <label data-for="username">username:</label>
            <input type="text" id="username" name="username" />
            <label data-for="password">password:</label>
            <input type="password" id="password" name="password" />
            <button type="submit">Sign in</button>
          </form>
          {/* เพิ่มส่วนแสดงข้อความ error */}
          {
            error ? <p>Error: {error}</p> : <div></div>
          }
        </div>
      )
}

export default SignInPage
```

## 6. ปรับเพิ่ม Session role

- เปิดไฟล์ **pages/api/auth/[...nextauth].ts**
- สร้าง type และแก้ไขส่วน callback ของ `NextAuth({})`
- ทดสอบในส่วน protected page

```tsx

import NextAuth, { Session, User } from "next-auth"
import CredentialsProvider from 'next-auth/providers/credentials'

// สร้าง type สำหรับใช้งานเพิ่ม role ให้กับ session
type SessionWithRole = {
  role:string
} & Session

export default NextAuth({
  //...
  callbacks: {
    // async jwt({ token, user }) {
    //   return token
    // },
    async session({session, user}) {
      (session as SessionWithRole).role = 'admin'
      return session
    }
  },
})