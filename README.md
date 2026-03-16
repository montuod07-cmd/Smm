<!DOCTYPE html>
<html>
<head>
    <title>Instagram Service Panel</title>
</head>
<body>

<h2>Instagram Service Panel</h2>

<div>
    <label>Instagram Username</label><br>
    <input id="user" type="text" placeholder="enter username"><br><br>

    <label>Select Service</label><br>
    <select id="service">
        <option value="likes">Post Likes</option>
        <option value="followers">Profile Promotion</option>
        <option value="views">Video Views</option>
    </select>
    <br><br>

    <label>Amount</label><br>
    <input id="amount" type="number" placeholder="enter number"><br><br>

    <button onclick="sendRequest()">Send Request</button>
</div>

<p id="msg"></p>

<script>
function sendRequest() {
    const user = document.getElementById("user").value;
    const service = document.getElementById("service").value;
    const amount = document.getElementById("amount").value;

    document.getElementById("msg").innerText =
        "Your request is saved. Connect this panel with your backend to process it.";
}
</script>

</body>
</html>
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
