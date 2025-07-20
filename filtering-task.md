---
layout: default
permalink: /filtering-task/
---

# Membuat katalog pencarian  menggunaakan filtering checkbox dengan react
dalam prject ini kita akan membagi dalam beberapa tahap
1. membuat dan mengecek state kumpulan checkbox, ambil state yang true which is yang di checked  -> menjadi list key yang akan dicek
2. filtering berdasarkan ketikan user di input + state yang true saja

### 1 Membuat state kumpulan checked box
sama seperti form jsx dimana kalau form jsx kan init state nya kan empty string ngumpulin semua input ke dalam 1 dict dan update valuenya di dalam dict itu sendiri
```jsx
  const [form,setFrorm] useState({
    nama:"",
    password:""
    
  })
   
```

sedangkan untuk checked box juga sama tapi init statenya boolean false (karena unchecked itu false)

```jsx
const [datachecked,setChecked] useState({
    nama:false,
    kota:false,
    perusahaan:false

  })
```

kemudian kita akan membuat check box dan inputnya text seperti biasa kita buat di return react
buat state untuk pencarian text terlebih dahulu
```jsx
  // buat state untuk menyimpan text pencarian
  const [search,setSearch] = useState("")
```

kemudian buat Ui nya dengan input dan checkbox


```jsx
 // state awal semua data
  const [dat,setData] = useState(people)

  return(
    <>
      <div className='Cont'>
        <h1>Latihan Filtering</h1>
        <div className='searchCont'>
          <label><h2>Cari:</h2></label>
          <input type='text' value={search} onChange={(e) => setSearch(e.target.value)}/>
        </div>
        <div className='searchCont' style={{flex:"0",display:"flex",flexDirection:"row-reverse"}}>
          <div className='checkBox'>
            <div>
              <label>by nama</label>
              <input type='checkbox' checked={datachecked.nama} />
            </div>
            <div>
              <label>by kota  :</label>
              <input type='checkbox' checked={datachecked.nama}/>
            </div>
            <div>
              <label>by perusahaan :</label>
              <input type='checkbox' checked={datachecked.nama}/>
            </div>
          </div>
        </div>
        <div style={{flex:"2", backgroundColor:'white',width:"95%",padding:"10px 20px"}}>
          <table>
            <thead>
            <tr>
              <th>Nama</th>
              <th>Kota</th>
              <th>Perusahaan</th>
            </tr>
          </thead>
          <tbody>
            {people.map((item,index) => (
              <tr key={index}>
                <td>{item.name}</td>
                <td>{item.city}</td>
                <td>{item.company}</td>


              </tr>
            ))}
          </tbody>
        </table>


        </div>
      </div>
```

untuk styling css tablenya sebagai berikut

```css
}
/* boder coolapse biar anatar baris saling berkesinambungan */
table {
  border-collapse: collapse;
  width: 100%;
}
/*ukuran baris dan kolom dalam tabel dikendalikan th sama td bukan tr */
th, td {
  border: 1px solid black;
  height: auto;
  text-align: center;
}
```

Tips:
- adjust size pada table itu kita pada th sama td. tr itu hanya bungkus
- row reversers flex direction dimulai dari kanan ke kiri
- pada jsx ada ini

```jsx
 <input type='text' value={search} onChange={(e) => setSearch(e.target.value)}/>
 ```
 kenapa langsung e.target.value karena kita tidak mengambil state sebelumnya untuk update
 
 ### 2. Handle logic filtering
 flow logicnya :
 1. check dalam state datachecked mana yang dicentang berarti bernilai true
 2. dari proses sebelumnya hasil yang diharapkan berbentuk list yang berisi key yang memiliki nilai true.

fundamental: karena state itu berebentuk object  -> mau check yang bernilai true -  kita harus extract nih menggunakan alat bantu entrie

```js
Object.entries(obj) //merubah ke bentuk [[key,value]]
```
sehingga dari sini array => .filtering value  == true  => .map extract key nya saja
tujuan saya disini ingin menampikan hasil filtering berdasarkan user ketik terlebih dahulu
jadi file .jsx

```jsx
const checkedTrue = Object.entries(datachecked)
      .filter((element) => element[1]== true) //k element index 0  dan value element index 1
      .map((element) => element[0])
  }
```
secara default filter memiliki

```js
array.filter((element, index, array) => {
  // element → item saat ini
  // index   → posisi item saat ini
  // array   → array asli lengkap yang sedang difilter
});
```

pada kode diatas param pertama itu item sehingga kita akses item pada array, sehingga item diatas kita bisa desctructing menjadi [k,v]

```js
const checkedTrue = Object.entries(datachecked)
      .filter(([k,v]) => v== true) 
      .map(([k,v]) => k)
  }
```
kemudian

```js
if (val.trim() == ""){
        setData(people)
        return;
    }
    // filter selalu filter data dari sumber asli data
    //karena item 1 object dan kita ingin akses semua value di item tersebut maka
    // array => 1 item object -> array all value di item -> string -> includes
    const filtered = people.filter((item) => Object.values(item)
        .join(" ")
        .toLowerCase()
        .includes(val.toLowerCase()))
    //updatestate buat ditampliint
    setData(filtered)
    
  }
```

jika input type text kosong akan menampilkan data original, kemudia kita bekerja dengan pencarian itu selalu menggunakan data original sebagai filtering, kemudian untuk kode ini jika input text tidak kosong
```js
const filtered = people.filter((item) => Object.values(item)
        .join(" ")
        .toLowerCase()
        .includes(val.toLowerCase()))
    //updatestate buat ditampliint
```
karena 1 item array = {} object maka kita ingin mengecek semua value dalam 1 baris -> extract semua value menggunakan object.values sehingga jadi array berisi semua values, lalu jadi string dengan perintah join dengan separate spasi kemudian menggunakan includes untuk check substring, jangan lupa semua di lowercase agar lebih aman.

Selanjtunya bagaimana menghandle filtering setelah kita check value true pada state checked
```js
 const filtered = checkedTrue.length !=0 
      ? people.filter((item) => {
        return (
            checkedTrue.some((nameKey) => {
            const valItem = item[nameKey];
            return valItem.toLowerCase().includes(val.toLowerCase())
          })
        )
      })
```

kode diatas jika checkbox ada checked kita melakukan filter, flow nya
1. filtering item, item di array berbentuk object
2. karena kita ingin menangkap keyname beberapa saja yang true kita ingin deklarasikan vallItem sesuai keyname, untuk mendeklarisikan arrow function di filter harus menggunakan {} dan didalamnya return 
3. masing2 item dimana object akan diseleksi field nya berdasarkan array field checked yang true aja, menggunakan some untuk mengecek minimal 1 bernilai true igunakan untuk mengecek apakah setidaknya satu elemen dalam array memenuhi kondisi tertentu. karena kita akan deklarasikan dalam some menggunakan return dan {}, pertama akses item sesuai keyname nya kemudian menggunakan includes untuk check substring, jika true minimal 1 maka akan return item, 

selanjutnya jika tidak ada checkedture maka hanya bergantung pada text box, seperti pada kode dibawah
```js
 })
      : people.filter((item) => Object.values(item)
          .join(" ")
          .toLowerCase()
          .includes(val.toLowerCase()))
```

full code nya
```js
 function handleChange(e){
    setSearch(e.target.value)
    const val = e.target.value;
    //check value == true
    const checkedTrue = Object.entries(datachecked)
      .filter((element) => element[1]== true)
      .map((element) => element[0])
    //logic pencarian
    //jika semua unchecked
    const filtered = checkedTrue.length !=0 
      ? people.filter((item) => {
        return (
            checkedTrue.some((nameKey) => {
            const valItem = item[nameKey];
            return valItem.toLowerCase().includes(val.toLowerCase())
          })
        )
      })
      : people.filter((item) => Object.values(item)
          .join(" ")
          .toLowerCase()
          .includes(val.toLowerCase()))
    //update
    setData(filtered)
  }
```

pada input checked bukan menggunakan e.target.value untuk updated tapi e.target.checked

jadi full code nya seperti ini 

```jsx
import { useState } from 'react'
import reactLogo from './assets/react.svg'
import viteLogo from '/vite.svg'
import './App.css'

function App() {
  const [datachecked,setChecked] = useState({
    name:false,
    city:false,
    company:false

  })
  // buat state untuk menyimpan text pencarian
  const [search,setSearch] = useState("")
  // data
  const people = [
  {
    name: "Andi Saputra",
    city: "Jakarta",
    company: "Tech Solusi Indonesia"
  },
  {
    name: "Siti Nurhaliza",
    city: "Bandung",
    company: "Bandung Digital Studio"
  },
  {
    name: "Rama Wijaya",
    city: "Surabaya",
    company: "Nusantara Coding House"
  },
  {
    name: "Dewi Kartika",
    city: "Yogyakarta",
    company: "Jogja Startup Hub"
  },
  {
    name: "Budi Santoso",
    city: "Jakarta",
    company: "GoTix Corp"
  },
  {
    name: "Maya Puspita",
    city: "Denpasar",
    company: "Bali Creative Lab"
  },
  {
    name: "Agus Pratama",
    city: "Medan",
    company: "Sumatera Teknologi"
  },
  {
    name: "Lina Marlina",
    city: "Makassar",
    company: "Makassar DevWorks"
  },
  {
    name: "Tono Hidayat",
    city: "Palembang",
    company: "Palembang Digital Partner"
  },
  {
    name: "Citra Anjani",
    city: "Semarang",
    company: "Semarang Creative Works"
  },
  {
    name: "Yusuf Rahman",
    city: "Bekasi",
    company: "Java Innovate"
  },
  {
    name: "Farah Nabila",
    city: "Depok",
    company: "Depok Web Studio"
  },
  {
    name: "Rizky Ramadhan",
    city: "Tangerang",
    company: "NextGen Solutions"
  },
  {
    name: "Intan Wulandari",
    city: "Pontianak",
    company: "Kalimantan App House"
  },
  {
    name: "Dani Firmansyah",
    city: "Balikpapan",
    company: "Borneo Techworks"
  }
] ;
  // state awal semua data
  const [dat,setData] = useState(people)
  function handleChange(e){
    setSearch(e.target.value)
    const val = e.target.value;
    //check value == true
    const checkedTrue = Object.entries(datachecked)
      .filter((element) => element[1]== true)
      .map((element) => element[0])
    //logic pencarian
    //jika semua unchecked
    const filtered = checkedTrue.length !=0 
      ? people.filter((item) => {
        return (
            checkedTrue.some((nameKey) => {
            const valItem = item[nameKey];
            return valItem.toLowerCase().includes(val.toLowerCase())
          })
        )
      })
      : people.filter((item) => Object.values(item)
          .join(" ")
          .toLowerCase()
          .includes(val.toLowerCase()))
    //update
    setData(filtered)
  }
    
  return (
    <>
      <div className='Cont'>
        <h1>Latihan Filtering</h1>
        <div className='searchCont'>
          <label><h2>Cari:</h2></label>
          <input type='text' value={search} onChange={handleChange}/>
        </div>
        <div className='searchCont' style={{flex:"0",display:"flex",flexDirection:"row-reverse"}}>
          <div className='checkBox'>
            <div>
              <label>by nama</label>
              <input type='checkbox' 
                checked={datachecked.name} 
                onChange={(e) => setChecked({...datachecked,name:e.target.checked})}/>
            </div>
            <div>
              <label>by kota  :</label>
              <input type='checkbox' 
                checked={datachecked.city}
                onChange={(e) => setChecked({...datachecked,city:e.target.checked})}
              />
            </div>
            <div>
              <label>by perusahaan :</label>
              <input type='checkbox' 
                checked={datachecked.company}
                onChange={(e) => setChecked({...datachecked,company:e.target.checked})}
              />
            </div>
          </div>
        </div>
        <div style={{flex:"2", backgroundColor:'white',width:"95%",padding:"10px 20px"}}>
          <table>
            <thead>
            <tr>
              <th>Nama</th>
              <th>Kota</th>
              <th>Perusahaan</th>
            </tr>
          </thead>
          <tbody>
            {dat.map((item,index) => (
              <tr key={index}>
                <td>{item.name}</td>
                <td>{item.city}</td>
                <td>{item.company}</td>


              </tr>
            ))}
          </tbody>
        </table>


        </div>
      </div>
    </>
  )

}

export default App

```

ketika handlechanged dijalankan maka akan filtering berdasarkan text dengan mempertimbangkan state dari checkedTrue, namun perhatikan pada function handle changed
```js
 const val = e.target.value;
```
disini kita karena secara real time kita akses langsung user inout dengan e.target.value. kenapa nggak menggunakan search pada useState state handle search text user, karena cara kerja react adalah

Aksi ==> event handler fungsi dijalankan ==> update state masuk antrian ==> event handler selesai dijalankan ==> update state ==> data.map (tampilkan)

jadi state nunggu update kalau eventhandler selesai dijalankan. pada kode diatas kita bisa filtering real time menggunakan text namun ketika checkbox di checked maka itu nggak berubah rl, baru berubah setelah ketik lagi setelah di checked

Bagaimana kita akan mengubah filtering realtime both text dan checkbox, bisa menggunakan useEffect() -> menjalankan efeksamping berdasarkan perubahan state saja

Misal user checked => dalam onchange  update change => rerender proses merubah UI berdasarkan state => ada efek samping state berubah maka fungsi dalam useEffect dijalankan => setData => rerender (misal data.map dijalankan)
jadi setiap updateState hsetelah itu - rerender - useEffect - update - rerender useeffect dijalankan ketika dependency nya berubah
jadi kode updatenya menjadi

```jsx
import { useEffect, useState } from 'react'
import reactLogo from './assets/react.svg'
import viteLogo from '/vite.svg'
import './App.css'
import { people } from './data/data'

function App() {
  const [datachecked,setChecked] = useState({
    name:false,
    city:false,
    company:false

  })
  // buat state untuk menyimpan text pencarian
  const [search,setSearch] = useState("")
    // state awal semua data
  const [dat,setData] = useState(people)

  //menggunakan useEffect
  useEffect(()=>{
    handleChange()
  },[search,datachecked])
  
  function handleChange(){
    //check value == true
    const checkedTrue = Object.entries(datachecked)
      .filter((element) => element[1]== true)
      .map((element) => element[0])
    //logic pencarian
    //jika semua unchecked
    const filtered = checkedTrue.length !=0 
      ? people.filter((item) => {
        return (
            checkedTrue.some((nameKey) => {
            const valItem = item[nameKey];
            return valItem.toLowerCase().includes(search.toLowerCase())
          })
        )
      })
      : people.filter((item) => Object.values(item)
          .join(" ")
          .toLowerCase()
          .includes(search.toLowerCase()))
    //update
    setData(filtered)
  }
    
  return (
    <>
      <div className='Cont'>
        <h1>Latihan Filtering</h1>
        <div className='searchCont'>
          <label><h2>Cari:</h2></label>
          <input type='text' value={search} onChange={(e) => setSearch(e.target.value)}/>
        </div>
        <div className='searchCont' style={{flex:"0",display:"flex",flexDirection:"row-reverse"}}>
          <div className='checkBox'>
            <div>
              <label>by nama</label>
              <input type='checkbox' 
                checked={datachecked.name} 
                onChange={(e) => setChecked({...datachecked,name:e.target.checked})}/>
            </div>
            <div>
              <label>by kota  :</label>
              <input type='checkbox' 
                checked={datachecked.city}
                onChange={(e) => setChecked({...datachecked,city:e.target.checked})}
              />
            </div>
            <div>
              <label>by perusahaan :</label>
              <input type='checkbox' 
                checked={datachecked.company}
                onChange={(e) => setChecked({...datachecked,company:e.target.checked})}
              />
            </div>
          </div>
        </div>
        <div style={{flex:"2", backgroundColor:'white',width:"95%",padding:"10px 20px"}}>
          <table>
            <thead>
            <tr>
              <th>Nama</th>
              <th>Kota</th>
              <th>Perusahaan</th>
            </tr>
          </thead>
          <tbody>
            {dat.map((item,index) => (
              <tr key={index}>
                <td>{item.name}</td>
                <td>{item.city}</td>
                <td>{item.company}</td>


              </tr>
            ))}
          </tbody>
        </table>


        </div>
      </div>
    </>
  )

}

export default App

```







