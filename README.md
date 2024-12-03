# AplikasiChris
mkdir ujian-app
cd ujian-app
mkdir backend
cd backend
npm init -y
npm install express mongoose body-parser cors dotenv
const express = require("express");
const dotenv = require("dotenv");
const mongoose = require("mongoose");
const cors = require("cors");

dotenv.config();

const app = express();
app.use(express.json());
app.use(cors());

mongoose.connect(process.env.MONGO_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
}).then(() => console.log("MongoDB Connected"))
  .catch(err => console.log(err));

app.use("/api/questions", require("./routes/questionRoutes"));
app.use("/api/results", require("./routes/resultRoutes"));

const PORT = process.env.PORT || 5000;

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
const mongoose = require("mongoose");

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });
    console.log("MongoDB connected");
  } catch (error) {
    console.error(`Error: ${error.message}`);
    process.exit(1);
  }
};

module.exports = connectDB;
const mongoose = require("mongoose");

const questionSchema = new mongoose.Schema({
  question: { type: String, required: true },
  options: { type: [String], required: true },
  answer: { type: String, required: true },
});

module.exports = mongoose.model("Question", questionSchema);
const mongoose = require("mongoose");

const resultSchema = new mongoose.Schema({
  name: { type: String, required: true },
  score: { type: Number, required: true },
  date: { type: Date, default: Date.now },
});

module.exports = mongoose.model("Result", resultSchema);
const express = require("express");
const router = express.Router();
const Question = require("../models/Question");

// GET semua soal
router.get("/", async (req, res) => {
  const questions = await Question.find();
  res.json(questions);
});

// POST soal baru
router.post("/", async (req, res) => {
  const { question, options, answer } = req.body;
  const newQuestion = new Question({ question, options, answer });
  await newQuestion.save();
  res.json(newQuestion);
});

module.exports = router;
const express = require("express");
const router = express.Router();
const Result = require("../models/Result");

// POST hasil ujian
router.post("/", async (req, res) => {
  const { name, score } = req.body;
  const result = new Result({ name, score });
  await result.save();
  res.json(result);
});

// GET semua hasil ujian
router.get("/", async (req, res) => {
  const results = await Result.find();
  res.json(results);
});

module.exports = router;

