# Amc
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>AMC Phase 1 – Exam Simulation</title>
<style>
body { font-family: Arial, sans-serif; background:#0f0f0f; color:#eaeaea; padding:20px; }
.question { margin-bottom:20px; padding:15px; background:#1b1b1b; border-radius:8px; }
.answers label { display:block; margin:6px 0; cursor:pointer; }
button { padding:10px 20px; font-size:16px; margin-top:20px; cursor:pointer; }
.result { font-size:22px; margin-top:30px; }
</style>
</head>
<body>

<h1>AMC Phase 1 – Exam Simulation (Hard)</h1>
<p>32 Questions · Single Correct Answer · Randomised</p>

<form id="exam"></form>
<button onclick="submitExam()">Submit Exam</button>

<div class="result" id="result"></div>

<script>
const questionPool = [
{
q: "Why must a circuit breaker not be reset without consulting the zone supervisor?",
a: ["It may still be hot","It could bypass MEL requirements","It may re-energise an isolated system unintentionally","Circuit breakers are single-use"],
c: 2
},
{
q: "Which colour identifies a Mandatory safety sign?",
a: ["Yellow","Red","Green","Blue"],
c: 3
},
{
q: "What is mandatory before carrying out maintenance on hydraulic systems?",
a: ["Temperature stabilisation","Fluid sampling","Depressurisation including reservoir head pressure","Leak inspection"],
c: 2
},
{
q: "Why are MEWP operations considered high risk around aircraft?",
a: ["Electrical interference","Composite sensitivity","Limited visibility and collision risk","Hydraulic instability"],
c: 2
},
{
q: "Which statement regarding safety wire is correct?",
a: ["It may be reused if undamaged","It must always be renewed once disturbed","Over-twisting increases safety","It is optional for self-locking nuts"],
c: 1
},
{
q: "Why must SKYDROL not be used in aircraft jacks?",
a: ["Low lubrication properties","Poor temperature resistance","Incompatible with jack seals","Excessive viscosity"],
c: 2
},
{
q: "What is the primary purpose of EWIS cleaning?",
a: ["Improve appearance","Reduce aircraft weight","Prevent arcing and fire risk","Provide corrosion protection"],
c: 2
},
{
q: "Which is a potential consequence of a bleed air leak?",
a: ["Hydraulic fluid loss","Structural damage and fire risk","Avionics software failure","Fuel imbalance"],
c: 1
},
{
q: "When is hearing protection mandatory?",
a: ["Above 70 dB","Above 75 dB","Above 80 dB","Above 90 dB"],
c: 2
},
{
q: "What must be done before opening an aircraft passenger door?",
a: ["Hydraulic pressure removed","Slide girt bar disarmed","Cabin lights off","Circuit breakers pulled"],
c: 1
},
{
q: "What is the function of a CAMO?",
a: ["Perform maintenance","Issue CRS","Manage continuing airworthiness","Train engineers"],
c: 2
},
{
q: "Which Part governs Maintenance Organisations?",
a: ["Part 21","Part 66","Part 145","Part 147"],
c: 2
},
{
q: "Who must an independent Quality Department report to?",
a: ["Base Maintenance Manager","Planning Department","Accountable Manager","EASA"],
c: 2
},
{
q: "What is the purpose of a CAME?",
a: ["Describe maintenance tasks","Detail CAMO operation in compliance with EASA","Record defects","Authorise engineers"],
c: 1
},
{
q: "Which action is required before cleaning EWIS components?",
a: ["Apply lubricant","Remove connectors","Open relevant circuit breakers","Increase ventilation"],
c: 2
},
{
q: "Why must grease be removed after lubrication?",
a: ["It attracts moisture","It increases temperature","It collects FOD and causes wear","It causes corrosion"],
c: 2
},
{
q: "Which document defines how an MRO conducts maintenance?",
a: ["AMP","CAME","MOE","MEL"],
c: 2
},
{
q: "What is the risk of using metal scrapers on aircraft surfaces?",
a: ["Static discharge","Surface damage","Corrosion acceleration","EWIS interference"],
c: 1
},
{
q: "Which waste must be disposed of in yellow bags?",
a: ["Hydraulic fluid","Fuel-soaked rags","Bio-hazard waste","Metal shavings"],
c: 2
},
{
q: "Why must locking pins be recorded in the Tech Log?",
a: ["For audit statistics","For inventory control","To ensure removal before flight","For warranty purposes"],
c: 2
},
{
q: "Which condition may cause aircraft systems to activate automatically during jacking?",
a: ["Ground mode","Maintenance mode","Air mode","Test mode"],
c: 2
},
{
q: "What is the correct cleaning rule for lubricated components?",
a: ["Grease-clean-grease","Clean-lube-leave","Clean-grease-clean","Flush-grease-flush"],
c: 2
},
{
q: "Which document must accompany chemicals in case of injury?",
a: ["AMM","SWPM","MSDS","MOE"],
c: 2
},
{
q: "What is the primary risk of FOD?",
a: ["Cosmetic damage","Operational delays","Damage, injury and safety violations","Increased maintenance cost"],
c: 2
},
{
q: "Why are safety men required during radar tests?",
a: ["Electrical hazards","Radiation exposure","Fuel vapour ignition","Noise exposure"],
c: 1
},
{
q: "What defines Line Maintenance?",
a: ["Hangar-based checks","Scheduled base checks","Maintenance on in-service aircraft outside hangars","Structural modifications"],
c: 2
},
{
q: "Which licence category permits CRS after base maintenance?",
a: ["Category A","Category B1","Category B2","Category C"],
c: 3
},
{
q: "Why must torque wrenches be checked on an Acratork?",
a: ["Speed","Convenience","Higher accuracy than wrench indication","EASA requirement"],
c: 2
},
{
q: "Which material must never be used to blank pipe ends?",
a: ["Plastic caps","Approved blanks","Tape or rags","Protective covers"],
c: 2
},
{
q: "What is the purpose of red 'REMOVE BEFORE FLIGHT' pennants?",
a: ["Visual warning","Weight reduction","Regulatory marking","Tool identification"],
c: 0
}
];

// Shuffle utility
function shuffle(array) {
for (let i = array.length - 1; i > 0; i--) {
const j = Math.floor(Math.random() * (i + 1));
[array[i], array[j]] = [array[j], array[i]];
}
return array;
}

const selectedQuestions = shuffle([...questionPool]).slice(0,32);
const examForm = document.getElementById("exam");

selectedQuestions.forEach((q, i) => {
const div = document.createElement("div");
div.className = "question";
const shuffledAnswers = shuffle(q.a.map((a, idx) => ({ text:a, idx })));
div.innerHTML = `<strong>Q${i+1}: ${q.q}</strong>`;
const answersDiv = document.createElement("div");
answersDiv.className = "answers";

shuffledAnswers.forEach(ans => {
answersDiv.innerHTML += `
<label>
<input type="radio" name="q${i}" value="${ans.idx}">
${ans.text}
</label>`;
});
div.appendChild(answersDiv);
examForm.appendChild(div);
});

function submitExam() {
let score = 0;
selectedQuestions.forEach((q, i) => {
const selected = document.querySelector(`input[name="q${i}"]:checked`);
if (selected && parseInt(selected.value) === q.c) score++;
});
const percent = ((score / 32) * 100).toFixed(1);
document.getElementById("result").innerHTML =
`✅ Correct: ${score} / 32<br>📊 Score: ${percent}%`;
}
</script>

</body>
</html>
