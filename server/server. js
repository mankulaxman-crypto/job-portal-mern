const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
require('dotenv').config();

const app = express();
app.use(cors());
app.use(express.json());

// MongoDB Connection
const MONGO_URI = process.env.MONGO_URI || "mongodb://localhost:27017/jobportal";
mongoose.connect(MONGO_URI).then(()=> console.log("MongoDB Connected")).catch(e=>console.log(e));

// User Schema
const UserSchema = new mongoose.Schema({
  name: String, email: {type:String, unique:true}, password: String, role: {type:String, default:"user"}
});
const User = mongoose.model('User', UserSchema);

// Job Schema
const JobSchema = new mongoose.Schema({
  title: String, company: String, location: String, salary: String,
  description: String, type: String, postedBy: String, createdAt: {type:Date, default:Date.now}
});
const Job = mongoose.model('Job', JobSchema);

// Auth Routes
app.post('/api/register', async (req,res)=>{
  try{
    const {name,email,password} = req.body;
    const hashed = await bcrypt.hash(password,10);
    const user = await User.create({name,email,password:hashed});
    res.json({message:"User Created"});
  }catch(e){ res.status(400).json({error:e.message}) }
});

app.post('/api/login', async (req,res)=>{
  const {email,password} = req.body;
  const user = await User.findOne({email});
  if(!user) return res.status(400).json({error:"User not found"});
  const isMatch = await bcrypt.compare(password,user.password);
  if(!isMatch) return res.status(400).json({error:"Invalid password"});
  const token = jwt.sign({id:user._id}, "secret123");
  res.json({token, user:{id:user._id, name:user.name, email:user.email}});
});

// Job Routes
app.get('/api/jobs', async (req,res)=>{
  const jobs = await Job.find().sort({createdAt:-1});
  res.json(jobs);
});

app.post('/api/jobs', async (req,res)=>{
  const job = await Job.create(req.body);
  res.json(job);
});

app.delete('/api/jobs/:id', async (req,res)=>{
  await Job.findByIdAndDelete(req.params.id);
  res.json({message:"Deleted"});
});

app.get('/', (req,res)=> res.send("Job Portal API Running"));

const PORT = process.env.PORT || 5000;
app.listen(PORT, ()=> console.log(`Server running on ${PORT}`));
