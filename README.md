##practical 1
A)
mkdir express-crud
npm install express mongoose
npm init -y

models/user.js
const mongoose = require ("mongoose")
const userSchema = new mongoose.Schema({
    name:String,
    age: Number
});

module.exports = mongoose.model("User", userSchema);

routers/userRouters
const express = require("express")
const User = require("../models/User")

const router = express.Router()

//POST
router.post("/",async(req,res)=>{
    let user = new User(req.body)
    await user.save()
    res.send("User Added")
})

//get
router.get("/", async(req, res)=>{
    let users = await User.find()
    res.json(users)
})

//put
router.put("/:id", async(req, res)=>{
    await User.findByIdAndUpdate(req.params.id, req.body)
    res.send("User update")

})

router.delete("/:id", async(req, res)=>{
    await User.findByIdAndDelete(req.params.id)
    res.send("user deleted")
})

module.exports = router;

###server.js
const express = require("express")
const mongoose = require("mongoose")

const UserRouter = require("./Routers/UserRouter")

const app = express();
app.use(express.json())

mongoose.connect("mongodb://127.0.0.1:27017/college")

.then(()=> console.log("MongoDb Connected"))

app.use("/users", UserRouter)

app.listen(3000, ()=>{
    console.log("server stated")
})


2] Perform File (image.doc) upload operation log using MongoDB.

1.) models/File.js
const mongoose = require("mongoose");

const fileSchema = new mongoose.Schema({
    filename: String,
    path: String
});

module.exports = mongoose.model("File", fileSchema);

2.) router/fileRouter.js

const express = require("express");
const multer = require("multer");
const File = require("../models/File");

const router = express.Router();

const upload = multer({ dest: "uploads/" });

// Upload file
router.post("/", upload.single("file"), async (req, res) => {
    const file = new File({
        filename: req.file.originalname,
        path: req.file.path
    });

    await file.save();

    res.send("File uploaded successfully");
});

module.exports = router;

3) server.js

const express = require("express");
const mongoose = require("mongoose");

const fileRouter = require("./router/fileRouter");

const app = express();

app.use(express.json());

mongoose.connect("mongodb://127.0.0.1:27017/college")
    .then(() => console.log("MongoDB connected"));

app.use("/file", fileRouter);

app.listen(3000, () => {
    console.log("Server started");
});

###Prac 2
Create chat application by using socket.Io

1. index.js
const express = require("express");
const http = require("http");
const { Server } = require("socket.io");

const app = express();
const server = http.createServer(app);
const io = new Server(server);

app.use(express.static(__dirname));

io.on("connection", (socket) => {
    console.log("User connected");

    socket.on("chat", (msg) => {
        io.emit("chat", msg);
    });
});

server.listen(3000, () => {
    console.log("Server running");
});

2. index.html

<!DOCTYPE html>
<html>
<head>
    <title>Chat App</title>
</head>
<body>

<h2>Chat Application</h2>

<input id="message" placeholder="Enter message">
<button onclick="send()">Send</button>

<ul id="chat"></ul>

<script src="/socket.io/socket.io.js"></script>

<script>

const socket = io();



function send() {

&#x20;   let msg = document.getElementById("message").value;

&#x20;   socket.emit("chat", msg);

}



socket.on("chat", (msg) => {

&#x20;   let li = document.createElement("li");

&#x20;   li.innerText = msg;

&#x20;   document.getElementById("chat").appendChild(li);

});


</body>
</html>



##practical3

server.js
const fs = require("fs")

fs.writeFile("output.txt", "Hello Everyone \nWelcome to node js", (err)=>{
    if(err){
        console.log("Writing Error")
    }
    else{
        console.log("File write successfully")

        fs.readFile("input.txt","utf-8", (err, data)=>{
            if(err){
                console.log("Reading error")
            }
            else{
                console.log("Content of input.txt")
                console.log(data)
            }
        })
    }
})
input.txt
hello everyone


###practical 4
To create a custom node.js module package of using npm test if locally and publish if to the npm  registry  so that if can be installed and used in other node.js projectshh
##mkdir my-user-modules
##cd my-user-module
##npm init-y

app.js
function addUser(name, email){
    return{
        name:name,
        email: email,
        message : "USer Added successfully"
}
}

function displayUser(user) {
    return "Name: " + user.name +
           "\nEmail: " + user.email +
           "\nMessage: " + user.message;
}

module.exports = {
    addUser,
    displayUser
}
test.js
const UserModule = require("./app")
const user= UserModule.addUser(
    "RAhul",
    "rahul@gmail.com"
)
console.log(UserModule.displayUser(user))

##npm login
npm package


##Practical 5
#mkdir student-management
##cd student-management
##npm init
##npm install express
server.js
const express = require("express")
const app= express()
const PORT= 3000

app.use(express.json())

let students=[
    {
        id:1,
        name:"Pratiksha",
        course:"BCA",
        email:"pratiksha@gmail.com"
    },
    {
        id:2,
        name:"Pratik",
        course:"BCom",
        email:"pratik@gmail.com"
    },
    {
        id:3,
        name:"Priya",
        course:"BSc CA",
        email:"priya@gmail.com"
    }
];

app.get("/",(req, res)=>{
    res.send("Welcom to the sudent Management api")
})

app.get("/students",(req, res)=>{
    res.json(students)
})

app.get("/students/:id",(req, res)=>{
    const id = parseInt(req.params.id)

    const student= students.find(student=>student.id===id)

    if(!student){
        return res.status(404).json({
            message:"Student not found"
        })

 
    }
    res.json(student)
})

app.post("/students", (req, res)=>{

    const newStudent={
        id:students.length+1,
        name:req.body.name,
        course:req.body.course,
        email:req.body.email
    }

    students.push(newStudent)

    res.status(201).json({
        message:"Student Added Succesfully.",
        student:newStudent
    })
})

app.put("/students/:id",(req,res)=>{
    const id= parseInt(req.params.id)
    const student=students.find(student=>student.id===id)

    if(!student){
        return res.status(404).json({
            message:"Student Not Found"
        })
    }

    student.name = req.body.name;
    student.course=req.body.course;
    student.email=req.body.email;

    res.json({
        message:"Student updated successfully",
        student:student
    })
})


app.delete("/students/:id",(req, res)=>{
    const id= parseInt(req.params.id)
    const index=students.findIndex(student=>student.id===id)

 
    if(index===-1){
        return res.status(404).json({
            message:"Student Not Found"
        })
    }
    const deletestudent= students.splice(index, 1)

    res.json({
        message:"Student deleted Successfully",
        student:deletestudent
    })
 
})

app.listen(PORT,()=>{
    console.log("Server started")
})


##Practical6
npm init -y
npm install express mongoose body-parse

//index.js
const express = require("express");
const mongoose = require("mongoose");

const app = express();

app.use(express.json());

mongoose.connect("mongodb://127.0.0.1:27017/studentDB")
    .then(() => console.log("MongoDB Connected"))
    .catch(err => console.log(err));

app.use("/users", require("./routes/userRoutes"));

app.listen(3000, () => {
    console.log("Server started");
});


//routes/userRoutes.js
const express = require("express");
const mongoose = require("mongoose");

const router = express.Router();

const User = mongoose.model(
    "User",
    new mongoose.Schema({
        name: String,
        email: String
    })
);

router.get("/", async (req, res) => {
    res.json(await User.find());
});

router.post("/", async (req, res) => {
    res.json(await User.create(req.body));
});

module.exports = router;
//http://localhost:3000/users


##Prac 7
Create a todolist using react

1. App.js
import React, { useState } from "react";
import TodoInput from "./TodoInput";
import TodoList from "./TodoList";

function App() {
    const [todos, setTodos] = useState([]);

    const addTodo = (task) => {
        setTodos([...todos, task]);
    };

    return (
        <div>
            <h1>To-Do List</h1>
            <TodoInput addTodo={addTodo} />
            <TodoList todos={todos} />
        </div>
    );
}

export default App;

2.Todoinuput.js

import React, { useState } from "react";

function TodoInput({ addTodo }) {
    const [task, setTask] = useState("");

    const add = () => {
        if (task !== "") {
            addTodo(task);
            setTask("");
        }
    };

    return (
        <div>
            <input
                value={task}
                onChange={(e) => setTask(e.target.value)}
                placeholder="Enter task"
            />
            <button onClick={add}>Add</button>
        </div>
    );
}

export default TodoInput;


3. To-do list.js

import React from "react";

function TodoList({ todos }) {
    return (
        <ul>
            {todos.map((todo, index) => (
                <li key={index}>{todo}</li>
            ))}
        </ul>
    );
}

export default TodoList;


Create and validate the user form in react.

App.js

import React, { useState } from "react";

function App() {
    const [name, setName] = useState("");
    const [email, setEmail] = useState("");
    const [error, setError] = useState("");

    const submitForm = (e) => {
        e.preventDefault();

        if (name === "" || email === "") {
            setError("Please fill all fields");
        } else if (!email.includes("@")) {
            setError("Enter a valid email");
        } else {
            setError("");
            alert("Form submitted successfully");
        }
    };

    return (
        <div>
            <h2>User Form</h2>

            <form onSubmit={submitForm}>
                <input
                    type="text"
                    placeholder="Enter name"
                    value={name}
                    onChange={(e) => setName(e.target.value)}
                />
                



                <input
                    type="email"
                    placeholder="Enter email"
                    value={email}
                    onChange={(e) => setEmail(e.target.value)}
                />
                



                <button type="submit">Submit</button>
            </form>

            <p>{error}</p>
        </div>
    );
}

export default App;


##practical 8
npx create-react-app my-app
cd my-app
//my-app/src/app.js
import logo from './logo.svg';
import './App.css';
import { useEffect, useState } from 'react';

function App() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/users")
      .then(res => res.json())
      .then(data => setUsers(data));
  }, []);

  return (
    <div>
      <h1>Users List</h1>
      <ul>
        {users.map(user =>(
          <li key={user.id}>
            <h3>{user.name}</h3>
            <p>Email: {user.email}</p>
            <p>City: {user.address.city}</p></li>
        ))}
      </ul>
    </div>
  );
}

export default App;
###npm start



##Practical 9
//npx create-react-app my-app
//npm init -y

import './App.css';
import { useState } from "react";

function App() {
  const [users, setUsers] = useState({ name: "", email: "" });
  const [message, setMessage] = useState("");

  const handleChange = (e) => {
    setUsers({ ...users, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();

    const res = await fetch("https://jsonplaceholder.typicode.com/users", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify(users)
    });

    setMessage(
      res.ok ? "Submitted successfully!" : "Failed to submit"
    );
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="name"
        onChange={handleChange}
        placeholder="Name"
        required
      />
      <br/><br/>
      <input
        name="email"
        onChange={handleChange}
        placeholder="Email"
        required
      />
      <br/><br/>
      <button type="submit">Submit</button>

      <h3>{message}</h3>
    </form>
  );
}

export default App;



##Practical 10
//npx create-react-app my-app
//npm i react-router-dom
//npm init -y

//home.js,contat.js,about.js
function contact(){
  return<h2>Home Page</h2>
}
export default contact


//app.js
import logo from './logo.svg';

import './App.css';
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

import Home from "./home";
import About from "./about";
import Contact from "./contact";

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link> /
        <Link to="/about">About</Link> /
        <Link to="/contact">Contact</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/About" element={<About />} />
        <Route path="/Contact" element={<Contact />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
