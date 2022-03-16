
# Progress status on Route Loading

> จะทำส่วนนี้ได้ [ต้องทำ workshop นี้ก่อน](../dynamic-ssg/README.md)

1. ติดตั้ง nprogress package

```
npm i 
npm i --save-dev @types/nprogress
```

2. สร้างไฟล์ **styles/nprogress.css**

```css
/* Make clicks pass-through */
#nprogress {
    pointer-events: none;
  }
  
  #nprogress .bar {
    background: #29d;
  
    position: fixed;
    z-index: 1031;
    top: 0;
    left: 0;
  
    width: 100%;
    height: 2px;
  }
  
  /* Fancy blur effect */
  #nprogress .peg {
    display: block;
    position: absolute;
    right: 0px;
    width: 100px;
    height: 100%;
    box-shadow: 0 0 10px #29d, 0 0 5px #29d;
    opacity: 1;
  
    -webkit-transform: rotate(3deg) translate(0px, -4px);
    -ms-transform: rotate(3deg) translate(0px, -4px);
    transform: rotate(3deg) translate(0px, -4px);
  }
  
  /* Remove these to get rid of the spinner */
  #nprogress .spinner {
    display: block;
    position: fixed;
    z-index: 1031;
    top: 15px;
    right: 15px;
  }
  
  #nprogress .spinner-icon {
    width: 18px;
    height: 18px;
    box-sizing: border-box;
  
    border: solid 2px transparent;
    border-top-color: #29d;
    border-left-color: #29d;
    border-radius: 50%;
  
    -webkit-animation: nprogress-spinner 400ms linear infinite;
    animation: nprogress-spinner 400ms linear infinite;
  }
  
  .nprogress-custom-parent {
    overflow: hidden;
    position: relative;
  }
  
  .nprogress-custom-parent #nprogress .spinner,
  .nprogress-custom-parent #nprogress .bar {
    position: absolute;
  }
  
  @-webkit-keyframes nprogress-spinner {
    0% {
      -webkit-transform: rotate(0deg);
    }
    100% {
      -webkit-transform: rotate(360deg);
    }
  }
  @keyframes nprogress-spinner {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
```

3. เปิดไฟล์​ **pages/_app.tsx**
4. ใช้ useEffect และ useRouter ในการกำหนด event ที่ต้องการแสดงตัว Loading

```tsx
import '../styles/globals.css'
import type { AppProps } from 'next/app'
import MainLayout from '../components/main-layout'
import { useEffect } from 'react'
import { useRouter } from 'next/router'

// import module และ css 
import NProgress from 'nprogress'
import './../styles/nprogress.css'

function MyApp({ Component, pageProps }: AppProps) {

  // เรียกใช้ router
  const router = useRouter()

  // ใช้ useEffect เพื่อให้ทำการแสดงตัวโหลดตอนที่มีการเปลี่ยน route 
  useEffect(() => {
    
    // function ที่จะทำงานตอน route change 
    const handleStart = (url:string) => {
      console.log(`Loading: ${url}`)
      setTimeout(()=>{}, 3000)
      NProgress.start()
    }

    // function ที่จะทำงานตอน route change เสร็จแล้ว
    const handleStop = () => {
      NProgress.done()
    }

    // กำหนด function ให้ event ของ route ตามที่ต้องการ
    router.events.on('routeChangeStart', handleStart)
    router.events.on('routeChangeComplete', handleStop)
    router.events.on('routeChangeError', handleStop)

    return () => {
      router.events.off('routeChangeStart', handleStart)
      router.events.off('routeChangeComplete', handleStop)
      router.events.off('routeChangeError', handleStop)
    }
  }, [router])


  return (
    <MainLayout>
      <Component {...pageProps} />
    </MainLayout>
  )
}

export default MyApp

```
