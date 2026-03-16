const express = require("express")
const cors = require("cors")
const bodyParser = require("body-parser")
const { v4: uuidv4 } = require("uuid")

const app = express()

app.use(cors())
app.use(bodyParser.json())

let orders = []

app.get("/", (req,res)=>{
    res.send("Social Media Panel API Running")
})

app.post("/create-order",(req,res)=>{

    const {username, service, amount} = req.body

    if(!username || !service || !amount){
        return res.status(400).json({message:"Missing data"})
    }

    const order = {
        id: uuidv4(),
        username: username,
        service: service,
        amount: amount,
        status: "pending",
        date: new Date()
    }

    orders.push(order)

    res.json({
        message:"Order created",
        order:order
    })

})

app.get("/orders",(req,res)=>{
    res.json(orders)
})

app.get("/order/:id",(req,res)=>{
    const order = orders.find(o=>o.id===req.params.id)

    if(!order){
        return res.status(404).json({message:"Order not found"})
    }

    res.json(order)
})

app.post("/update-status",(req,res)=>{

    const {id,status} = req.body

    const order = orders.find(o=>o.id===id)

    if(!order){
        return res.status(404).json({message:"Order not found"})
    }

    order.status = status

    res.json({
        message:"Order updated",
        order:order
    })
})

app.delete("/delete-order/:id",(req,res)=>{
    orders = orders.filter(o=>o.id!==req.params.id)

    res.json({message:"Order deleted"})
})

app.listen(3000, ()=>{
    console.log("Server running on port 3000")
})
