---
layout: default
permalink: /crud/
---



# Membuat CRUD update dan delete menggunakan REDUX sederhana
- Pertama install tailwind buat styling
```cmd
npm install -D tailwindcss@3.4.1 postcss autoprefixer
```

habis itu generate confignya dan kita atur config nya, menggunakan
```cmd
npx tailwindcss init -p
```
atur config nya di content, file tailwind dimana yang diload dalam file kita isikan seperti ini
```js
/** @type {import('tailwindcss').Config} */
export default {
  //isi content css file ke load dimana aja
  content: [ 
    './index.html',
    './src/**/*.{jsx}' // dalam folder src dan namafilenya jsx
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}

```
buat file di src/input.css isikan disini untuk bisa menggunakan styling css pada file kita
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

```

##### Buat file nya buat folder dalam src layout,
digunakan sebagai main global layout yang biasanya berisi sidebar footer header dkk
- buat file MainLayout
```js
export default function MainLayout(){
    return (
        <>
            <div className="w-screen h-screnn flex flex-row"></div>
                {/* sidebar */}
                <div className="fixed w-[20%] h-screen shadow-lg bg-blue-200">

                </div>

                {/* content */}
                <div className="ml-[20%] w-[80%] flex flex-col px-4 py-2">
                    <div className="w-full h-[15%] flex flex-row px-4 justify-between items-center">
                        <h1 className="text-blue-200 text-5xl font-bold">Welcome to My Page</h1>
                        <button className="h-full w-[20%] flex flex items-center justify-center bg-red-200 rounded-xl">Buat Post</button>
                    </div>
                </div>
        
        
        </>
    )
}
```
kemudian didalam APP.JSX import mainlayout di dalamaya agar bisa ditampilan

#### ADD POST
dalam file di atas ada button untuk add post aku mau ketika click button tersebut kita buka form nya, makanya kita butuh route yang bisa pintah langkah langkahnya
- install BrowserRoute 
```cmd
npm install react-router-dom
```
- wrap button dengan Link
import dulu import {Link} from 'react-router-dom' kemudian 
```jsx
<Link to="/post"><button className="h-full w-[20%] flex flex items-center justify-center bg-red-200 rounded-xl">Buat Post</button></Link>
```
bedanya menggunakan "/" dan tidak
-   / ntuk hpindah ke halaman utama 
-   tanpa / untuk pindah dalam subpage pada halaman utama

kalau kita ingin pindah dari root satu ke root lainnya gunakan  ini /
```jsx
<div className="ml-[20%] w-[80%] flex flex-col px-4 py-2">
                    <div className="w-full h-[15%] flex flex-row px-4 justify-between items-center">
                        <h1 className="text-blue-200 text-5xl font-bold">Welcome to My Page</h1>
                        <Link to="/post" className="h-full w-[20%] flex flex items-center justify-center bg-red-200 rounded-xl transition-all animation-300 hover:bg-blue-200"><button>Buat Post</button></Link>
                    </div>
</div>
```
terus di app.jsx kita ubah sepert ini
```jsx

import MainLayout from "./layout/MainLayout"
import PostForm from './pages/PostForm'
//jangan lupa ketika SPA gunakan ini
import { BrowserRouter, Routes, Route  } from "react-router-dom"
export default function App() {
  return (
    <BrowserRouter>
       {/**buat loccalohost rootnya */}
       {/* anak dari subpage adalah post */}
      <Routes>
        <Route path='/' element={<MainLayout/>}/> 

        {/* halaman postform tampil terpisah, fullscreen */}
        <Route path='/post' element={<PostForm/>} />
        

      </Routes>
    
    </BrowserRouter>
  )
}

```

#### Buat ADD form Post
dalam folder pages buat form untuk ambil data dari user
```jsx
import { useState } from "react"
// impoert reducer nya
import {addData} from '../models/FormSlice'
import { useDispatch } from "react-redux"
import { useNavigate } from "react-router-dom";
export default function PostForm(){
    //panggil dengan dispatch
    const dispatch = useDispatch();
    //navigasi 
    const navigate = useNavigate()
    // tempat form
    const [data,setData] = useState({
        title:"",
        content:""
    })
    //ketika button diclick jalankan ini
    function submitData(e){
        e.preventDefault()
        //jika kosong
        if(data.title == "" || data.content == ""){
            alert('isi lengkap fromnya')
            //tanpa return kode diular if akan dijalankan makanya kita hentikan dengan wrap if saja dengan return
            return;
        }
        // jika tidak
        //ke redux
        dispatch(addData(data))
        //reset
        setData({
            title:"",
            content:""
        })
        //navigasi ke "/"
        navigate("/")


        
    }
    return(
        <>
            <div className="mx-auto mt-[10vh] border border-black rounded-lg flex flex-col items-center h-[60vh] w-[30%] shadow-lg">
                <h1 className="text-2xl">Form data</h1>
                <form onSubmit={submitData} className="flex flex-col items-center w-full h-full">
                    <div className="w-full h-[20%]  px-2 flex flex-row items-center gap-11 box-border">
                        <label>Title</label>
                        <input className="h-1/2 w-full border border-black" value={data.title} onChange={(e) => setData({...data,title:e.target.value})}/>

                    </div>
                     <div className="w-full h-[50%]  px-2 flex flex-row items-start gap-4">
                        <label>Content</label>
                        <textarea className="h-3/4 w-full border border-black" value={data.content} onChange={(e) => setData({...data,content:e.target.value})}/>

                    </div>
                    <div className="w-full h-[20%]  borderpx-2 flex flex-row items-center justify-center  box-border">
                        <button type='submit' className="h-1/2 w-[30%] border border-black rounded hover:scale-[1.2] transition-all duration-300">Submit Data</button>
                    </div>

                    
                </form>
            </div>
        
        
        </>
    )
}
```
buat redux jangan lupa dengan membuat slice kemudian diikuti dengan store nya, navigasi kembali dengan menggunakan react router dom ke page awal yang beralamat /

#### menampilka data
kita panggil dengan menggunakan useSelector dan kita show datanya dengan menggunakan map
```jsx
// import react router dom
import {Link} from 'react-router-dom'
import CardItem from '../components/cardItem'
import { useSelector } from 'react-redux'
//jangan lupa menggunakan export default

export default function MainLayout(){
    //panggil data
    const data = useSelector((state) => state.formdata)

    return (
        <>
            <div className="w-screen h-screnn flex flex-row"></div>
                {/* sidebar */}
                <div className="fixed w-[20%] h-screen shadow-lg bg-blue-200">

                </div>

                {/* content */}
                <div className="ml-[20%] w-[80%] flex flex-col px-4 py-2">
                    <div className="w-full h-[15%] flex flex-row px-4 justify-between items-center">
                        <h1 className="text-blue-200 text-5xl font-bold">Welcome to My Page</h1>
                        <Link to="/post" className="h-full w-[20%] flex flex items-center justify-center bg-red-200 rounded-xl transition-all animation-300 hover:bg-blue-200"><button>Buat Post</button></Link>
                    </div>
                    <div className={`w-full mt-7 min-h-screen boder border-black ${data.length == 0 ? "flex flex-col items-center justify-center" : "flex flex-row"}`}>
                         {data.length == 0
                            ? <h1 className='text-4xl'>Tidak ada data</h1>
                            : data.map((value,index) => (

                                <CardItem item={value} indexItem ={index} key={index} />


                            ))
                        }
                    </div>
                   
                </div>
        
        
        </>
    )
}
```
catatan :
- mengguankan ternari if-else pada kedua classname dan mapping nya
- membuat komponent card item kita ngirim datanya dan index nya
- pada card item

```jsx
// import react router dom
import {Link} from 'react-router-dom'
import CardItem from '../components/cardItem'
import { useSelector } from 'react-redux'
//jangan lupa menggunakan export default

export default function MainLayout(){
    //panggil data
    const data = useSelector((state) => state.formdata)

    return (
        <>
            <div className="w-screen h-screnn flex flex-row"></div>
                {/* sidebar */}
                <div className="fixed w-[20%] h-screen shadow-lg bg-blue-200">

                </div>

                {/* content */}
                <div className="ml-[20%] w-[80%] flex flex-col px-4 py-2">
                    <div className="w-full h-[15%] flex flex-row px-4 justify-between items-center">
                        <h1 className="text-blue-200 text-5xl font-bold">Welcome to My Page</h1>
                        <Link to="/post" className="h-full w-[20%] flex flex items-center justify-center bg-red-200 rounded-xl transition-all animation-300 hover:bg-blue-200"><button>Buat Post</button></Link>
                    </div>
                    <div className={`w-full mt-7 min-h-screen boder border-black ${data.length == 0 ? "flex flex-wrap flex-col items-center justify-center" : "flex flex-wrap flex-row"}`}>
                         {data.length == 0
                            ? <h1 className='text-4xl'>Tidak ada data</h1>
                            : data.map((value,index) => (

                                <CardItem item={value} indexItem ={index} key={index} />


                            ))
                        }
                    </div>
                   
                </div>
        
        
        </>
    )
}
```
kita buat 1 reducer untuk filter data berdasarkan user hapus datanya lagi agar dapat diupdate bentuk reducer na kayak gini, terus kita juga menggunakan flex-wrap untuk ganti baris item jika sudah mencapai ujung dari container
```js
import {createSlice} from '@reduxjs/toolkit'
import { useState } from 'react'
// buat nampung data
const FormSlice = createSlice({
    name:'form',
    initialState:[

    ],
    reducers:{
        addData : (state,action) => {
            state.push(action.payload)
        },
        // add delete data
        deleteData : (state,action) => {
           return state.filter((_,index) => index != action.payload)
        }

    }
})

// export reducer
export const {addData,deleteData} = FormSlice.actions

// IMPORT slice ke store
export default FormSlice.reducer
```

#### Menambah Update Form
```jsx
import { useState } from "react"
// impoert reducer nya
import {updateDat} from '../models/FormSlice'
import { useDispatch, useSelector } from "react-redux"
import { useNavigate, useParams } from "react-router-dom";
export default function UpdateForm(){
    //panggil dengan dispatch
    const dispatch = useDispatch();
    //navigasi 
    const navigate = useNavigate()

    //karena update:id maka akse url dengan useParams
    const {id} = useParams()
    //jangan lupa di parseInt

    // ambil data
    const alldata = useSelector((state) => state.formdata)
    
    //filter menghasilkan array dan akses valuenya menggunakan index
    const datFilter = alldata.filter((_,index) => index == parseInt(id))[0]

    const [data,setData] = useState({
        title:datFilter.title ,
        content:datFilter.content,
        id:parseInt(id)
    })
    console.log(datFilter)
    // ketika button diclick jalankan ini
    function UpdateData(e,id){
        e.preventDefault()
        //jika kosong
        if(data.title == "" || data.content == ""){
            alert('isi lengkap fromnya')
            //tanpa return kode diular if akan dijalankan makanya kita hentikan dengan wrap if saja dengan return
            return;
        }
        // jika tidak
        //ke redux
        dispatch(updateDat(data))
        //reset
        setData({
            title:"",
            content:"",
            id:""
        })
        //navigasi ke "/"
        navigate("/")


        
    }
    return(
        <>
            <div className="mx-auto mt-[10vh] border border-black rounded-lg flex flex-col items-center h-[60vh] w-[30%] shadow-lg">
                <h1 className="text-2xl">Form data</h1>
                <form  onSubmit={UpdateData}className="flex flex-col items-center w-full h-full">
                    <div className="w-full h-[20%]  px-2 flex flex-row items-center gap-11 box-border">
                        <label>Title</label>
                        <input className="h-1/2 w-full border border-black" value={data.title} onChange={(e) => setData({...data,title:e.target.value})}/>

                    </div>
                     <div className="w-full h-[50%]  px-2 flex flex-row items-start gap-4">
                        <label>Content</label>
                        <textarea className="h-3/4 w-full border border-black" value={data.content} onChange={(e) => setData({...data,content:e.target.value})}/>

                    </div>
                     <div className="w-full h-[20%]  borderpx-2 flex flex-row items-center justify-center  box-border">
                        <button type='submit' className="h-1/2 w-[30%] border border-black rounded hover:scale-[1.2] transition-all duration-300">Update Data</button>
                    </div>

                    
                </form>
            </div>
        
        
        </>
    )
}
```
catatan pada app.jsx
- alamat update/:id ditangkap pada form ini dengan const {id} = useParams, nama harus sama dengan router path nya, jangan lupa habis itu di parse int
pada main layout tombolnya seperti ini navigasi ke
```jsx
<button className="flex flex-1 items-center justify-center border border-black" onClick={() => navigate(`update/${indexItem}`)}>update</button>
```



