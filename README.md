# i-Hack-Gaming-Hackathon-Sujay_Roy-
The Indian online gaming industry is booming, offering opportunities for entertainment, skill growth, and economic impact. However, challenges like online harassment, data breaches, fraud, addiction, and lack of gameplay transparency persist.
1. Backend (Node.js with Express)
# Install Dependencies:
# bash
npm install express mongoose bcryptjs jsonwebtoken cors dotenv

# server.js

# javascript

const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const dotenv = require('dotenv');

dotenv.config();

const app = express();
app.use(cors());
app.use(express.json());

// Connect to MongoDB
mongoose.connect(process.env.MONGODB_URI, { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log('MongoDB connected'))
    .catch(err => console.log(err));

// User model
const UserSchema = new mongoose.Schema({
    username: String,
    password: String,
    email: String,
    isVerified: { type: Boolean, default: false },
    // Additional fields for KYC
});

const User = mongoose.model('User ', UserSchema);

// User Registration
app.post('/api/register', async (req, res) => {
    const { username, password, email } = req.body;
    const hashedPassword = await bcrypt.hash(password, 10);
    const newUser  = new User({ username, password: hashedPassword, email });
    await newUser .save();
    res.status(201).send('User  registered');
});

// User Login
app.post('/api/login', async (req, res) => {
    const { username, password } = req.body;
    const user = await User.findOne({ username });
    if (user && await bcrypt.compare(password, user.password)) {
        const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET, { expiresIn: '1h' });
        res.json({ token });
    } else {
        res.status(401).send('Invalid credentials');
    }
});

// Responsible Gaming: Set Limits
app.post('/api/set-limit', async (req, res) => {
    const { userId, limit } = req.body;
    await User.findByIdAndUpdate(userId, { limit });
    res.send('Limit set');
});

// Start the server
const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));




# 2. Frontend (React.js)
Install Dependencies:

npx create-react-app gaming-app
cd gaming-app
npm install axios react-router-dom

# src/App.js

# javascript

import React from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import Register from './components/Register';
import Login from './components/Login';
import Dashboard from './components/Dashboard';

function App() {
    return (
        <Router>
            <Switch>
                <Route path="/register" component={Register} />
                <Route path="/login" component={Login} />
                <Route path="/dashboard" component={Dashboard} />
            </Switch>
        </Router>
    );
}

export default App;


# src/components/Register.js

import React, { useState } from 'react';
import axios from 'axios';

const Register = () => {
    const [username, setUsername] = useState('');
    const [password, setPassword] = useState('');
    const [email, setEmail] = useState('');

    const handleSubmit = async (e) => {
        e.preventDefault();
        await axios.post('http://localhost:5000/api/register', { username, password, email });
        alert('User  registered');
    };

    return (
        <form onSubmit={handleSubmit}>
            <input type="text" placeholder="Username" onChange={(e) => setUsername(e.target.value)} required />
            <input type="email" placeholder="Email" onChange={(e) => setEmail(e.target.value)} required />
            <input type="password" placeholder="Password" onChange={(e) => setPassword(e.target.value)} required />
            <button type="submit">Register</button>
        </form>
    );
};

export default Register;

# src/components/Login.js

import React, { useState



# All Code in Javascript
