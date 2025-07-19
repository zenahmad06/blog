---
layout: page
title: "Membuat Todo List dengan React"
permalink: /blog/todo-list/
---

# Membuat todo list sederhana
### 1. Membuat form input
Buat component folder terlebih dahulu di dalam src di vs code dan kemudian buat jsx components nya bernama form-insert.jsx
```jsx
 <>
     <form onSubmit={handleSub} className={styled.formCont}>
        <label>Isikan Task :</label>
        <input required type="text" value={form.task} onChange={(e) => setForm({...form,task:e.target.value})}/>
        <button type="submit">add task</button>
    </form>
</>
ini untuk styling taruh di folder styles dalam src
```
- e.target value digunakan untuk mendengar inputan dari user taruh di param onChange
- value = {form.task} => untuk menampikan apa yang diketik di user
- form terdapat param onSubmit untuk mengirim data di dalam form button harus bertype submit
```css
.formCont{
    height: 20vh;
    width: 50%;
    margin: 0 auto;
    border: 1px solid black;
    display:  flex;
    align-items: center;
    gap: 5px;

}
.formCont input{
    flex: 1;
    height: 40%;
    border-radius: 10px;
    font-size: 16pt;
}
.formCont button{
    width: 9%;
    margin-right: 5px;

}
.formCont label{
    margin-left: 5px;
}
dalam form ini kita akan menggunakan useState untuk menghandle data yang diinput oleh user. Nantinya ini akan dikirim ke parent. 
```jsx
import { useState } from "react"
import styled from '../styles/form.module.css'
export default function InsertForm({onSend}){
    //definiskan state buat simpan form
    const [form,setForm] = useState({
        time: Date.now(),
        task:"",
        completed:false
    });
    // tombol
    function handleSub(e){
        e.preventDefault()
        onSend(form) // kirim ke parent
        //reset
        setForm({
            time: Date.now(),
            task:"",
            completed:false
        });

    }
```
- function InserForm memiliki param key OnSend jadi di dalam parent nanti component inserForm nya harus memiliki key onSend seperti ini :
```jsx
<InsertForm onSend={updateData}/>
```
- buat state form dan fungsi untuk update bernama setForm dan definisikan state awaknya
- Date.now untuk record waktu sekarang
- nilai default completed adalah false
- e.preventDefault mencegah reload setiap kali kita click tombol submit
- onSubmit isikan fungsi yang dikerjakan pada handleSub karena kita ingin mengirim ke parent maka panggil fungsi yang berada di onSend (dengan ini parent sudah memiliki parameter sehingga bisa update state di parent)
- setelah di send kita reset kembalik dengan task dikembalikan ke empty

### 2. dalam Aapp.jsx dimana ini parentnya import component insertForm
```jsx
import { useState } from 'react'
import './App.css'
import InsertForm from './components/form-insert'

```
- ./ artinya app.jsx 1 root dengan folder components, ../ kalau ini kita harus naik 1 folder dari child ke parent folder .
kemudian dalam app.jsx write code
```jsx
function App() {
  const [data, setData] = useState([])
  function updateData(val){
    //mau push val ke existing data
    //karena kita butuh data sebelumnya maka butuh fungsi di setData
    setData((previous) => {
      //copy
      const datCp = [...previous];
      // add val menggunakan pust
      datCp.push(val);
      return datCp //UPDATE data dengan updatean

    })
  }
```
- karena onSend data itu updateVal(val) val itu param disini untuk menerima value dari child ke param
- karena kita akan menggunakan previous state untukk update maka gunakan fungsi untuk update datanya 
- dalam fungsi tersebut lebih aman kita copy menggunakan spread operator dan update copyannnya tadi dengan .push (karena array state data globalnya)
- fungsi menggunakan {} harus ada kata "return didalamanya"
kemudian render di app terdiri dari inserform dan tampilan opsional berdasarkan data state
```.jsx
return (
    <>
      <InsertForm onSend={updateData}/>
      <div>
        {data.length != 0 
          ? data.map((item,index) => (
            <div key={index}>
              <div>{item.task}</div>
              <button onClick={() => deleteItem(index)}>hapus</button>
              <button onClick={() => changeItem(index)}>completed</button>

            </div>
          ))
          : <h1>belum ada</h1>
        }

      </div>
    </>
```
- menggunakan map untuk menampilakn semua data
-  didalam {} tampilakn jika kondisi state.length tidak kosong maka ? true jalankan mapping kemudian di map default memiliki param index dan item,  index digunakan di div container masing2 item gunaya untuk menandai item dan item adalah data di dalam []
- {item.task} untuk menangkap apa yang ada di item
- terdaoat button delete ketika diclick onClick harus menggunakan fungsi untuk menjalankan fungsi lain, deleteiTEM MEMILIKI param index digunakan untuk menghapus existing item
```jsx
    //karena butuh current sebelumnya maka
  function deleteItem(val){
    setData((previous) => {
      //copy arr
      const arr_copy = [...previous]; //copy terlebih dahulu
      //menggunakan filter untuk eliminasi
      const filteredArr = arr_copy.filter((_,index) => index != val) //menggunakan filter
      //filter itu membentu array baru ya
      return filteredArr
    })
  }
```
- di dalam item juga ada tombol completed untuk merubah key pada data false ke true, onClick menjalankan ini
```jsx
 //fungsi untuk merubha status
  function changeItem(x){
    setData((previous) => {
      const arr = [...previous];
      // cari dengan find
      const search = arr.find((item,index) => index == x)
      // find mencari 1 data aja hasilnya object {}
      if(search){
        search.completed = true; //ubah ke true
        // otomatis juga merubah parent []
      }
      console.log(arr)
      return arr;
    })
  }
```
- find -> mencari 1 data aja dalam [], jika ingin merubah data setelah find maka akan merubah data di parenntya, sehingga parent otomati update data
pada app,jsx tambahkan checklist
```jsx
     <div className={styled.status}>
              <span style={{visibility:item.completed ? "visible" : "hidden"}}>
                ✔</span>
              </div>
```
- selalu render container checklist namun di hiddent
- menggunakan ternary if kita bisa merubah style berdasarkan kondisi
- untuk stylingnya menggunakan proporsi ini salah satu kenggulan display:flex
- klick window + . untuk akses symbol
```css
.task{
    flex: 2; /* karen parent display flex, width child menggunkana proporsi menagatur width */
}
.status{
    width: 20px;
    margin-left: 5px;
}
```
### tips stlylinh
- gunakan flex di parent agar kita bisa atur width di child dengan flex proporsi
- terus di parent , gunakan height auto atau minheight agar menyesuaikan konten di dalamnya
