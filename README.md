require("dotenv").config();
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");

const app = express();
app.use(express.json());
app.use(cors());

mongoose
  .connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log("MongoDB Connected"))
  .catch((err) => console.log(err));

app.use("/api/users", require("./routes/userRoutes"));
app.use("/api/lands", require("./routes/landRoutes"));
app.use("/api/chat", require("./routes/chatRoutes"));

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(
Server running on port ${PORT}));

Configure MongoDB & Express (server.js)

const mongoose = require("mongoose");

const UserSchema = new mongoose.Schema({
  name: String,
  email: { type: String, unique: true },
  password: String,
  role: { type: String, enum: ["cultivator", "landowner", "expert"], default: "cultivator" }
});

module.exports = mongoose.model("User", UserSchema);

User authentication

const express = require("express");
const bcrypt = require("bcryptjs");
const jwt = require("jsonwebtoken");
const User = require("../models/User");

const router = express.Router();

// Register
router.post("/register", async (req, res) => {
  const { name, email, password, role } = req.body;
  const hashedPassword = await bcrypt.hash(password, 10);
  const user = new User({ name, email, password: hashedPassword, role });

  try {
    await user.save();
    res.status(201).json({ message: "User registered successfully!" });
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
});

// Login
router.post("/login", async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email });

  if (!user || !(await bcrypt.compare(password, user.password))) {
    return res.status(400).json({ error: "Invalid credentials" });
  }

  const token = jwt.sign({ userId: user._id }, process.env.JWT_SECRET, { expiresIn: "1h" });
  res.json({ token, user });
});

module.exports = router;
UserRoutes.js

const mongoose = require("mongoose");

const LandSchema = new mongoose.Schema({
  owner: { type: mongoose.Schema.Types.ObjectId, ref: "User" },
  location: String,
  soilType: String,
  size: String,
  price: Number,
  available: { type: Boolean, default: true },
});

module.exports = mongoose.model("Land", LandSchema);

Land listing API

const express = require("express");
const Land = require("../models/Land");

const router = express.Router();

// Add Land
router.post("/", async (req, res) => {
  const land = new Land(req.body);
  try {
    await land.save();
    res.status(201).json({ message: "Land added successfully!" });
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
});

// Get Lands
router.get("/", async (req, res) => {
  const lands = await Land.find().populate("owner");
  res.json(lands);
});

module.exports = router;

landRoutes.js

import axios from "axios";

const API = axios.create({ baseURL: "http://localhost:5000/api" });

export const register = (data) => API.post("/users/register", data);
export const login = (data) => API.post("/users/login", data);
export const getLands = () => API.get("/lands");

API calls

scr/api.js

import React, { useEffect, useState } from "react";
import { getLands } from "../api";

const LandList = () => {
  const [lands, setLands] = useState([]);

  useEffect(() => {
    getLands().then((res) => setLands(res.data));
  }, []);

  return (
    <div className="container">
      <h2>Available Lands</h2>
      <div className="row">
        {lands.map((land) => (
          <div className="col-md-4" key={land._id}>
            <div className="card">
              <div className="card-body">
                <h5 className="card-title">{land.location}</h5>
                <p>Soil Type: {land.soilType}</p>
                <p>Size: {land.size} acres</p>
                <p>Price: ${land.price}</p>
                <p>{land.available ? "Available" : "Not Available"}</p>
              </div>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
};

export default LandList;

Pages and components 

scr/pages/LandList.js

import React from "react";
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import LandList from "./pages/LandList";

function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<LandList />} />
      </Routes>
    </Router>
  );
}

export default App;

Setup react Router

src/App.js
