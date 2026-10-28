# MediaLab.github.io

<!DOCTYPE html>
<html lang="da">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MediaLab Åbningstider</title>

<style>

*{
    margin:0;
    padding:0;
    box-sizing:border-box;
}

body{
    font-family:Arial, sans-serif;
    background:#f4f6f9;
    color:#333;
}

.top-links{
    margin-top:15px;
    display:flex;
    justify-content:center;
    gap:12px;
    flex-wrap:wrap;
}

.top-links a{
    color:white;
    text-decoration:none;
    font-size:0.9rem;
    padding:6px 12px;
    border-radius:20px;
    background:rgba(255,255,255,0.15);
    transition:0.2s;
}

.top-links a:hover{
    background:rgba(255,255,255,0.25);
}

header{
    background:#1f2937;
    color:white;
    text-align:center;
    padding:40px 20px;
}

header h1{
    margin-bottom:10px;
}

.container{
    max-width:1200px;
    margin:40px auto;
    padding:0 20px;
}

.schedule{
    display:grid;
    grid-template-columns:repeat(auto-fit,minmax(250px,1fr));
    gap:20px;
}

.card{
    background:white;
    border-radius:16px;
    padding:25px;
    position:relative;
    box-shadow:0 4px 10px rgba(0,0,0,0.1);
    transition:0.3s;
}

.card:hover{
    transform:translateY(-5px);
}

.today{
    border:3px solid #22c55e;
    background:#f0fff4;
}

.today-badge{
    position:absolute;
    top:15px;
    right:15px;
    background:#22c55e;
    color:white;
    padding:5px 10px;
    border-radius:20px;
    font-size:12px;
    font-weight:bold;
}

.day{
    font-size:1.3rem;
    font-weight:bold;
    margin-bottom:10px;
}

.date{
    color:#777;
    margin-bottom:15px;
}

.person{
    font-size:1.1rem;
    margin-bottom:8px;
}

.time{
    color:#22c55e;
    font-weight:bold;
    font-size:1.1rem;
}

.closed{
    color:#ef4444;
    font-weight:bold;
}

.status-dot{
    display:inline-block;
    width:12px;
    height:12px;
    border-radius:50%;
    margin-right:8px;
}

.green{
    background:#22c55e;
}

.red{
    background:#ef4444;
}

.loading{
    text-align:center;
    grid-column:1/-1;
    padding:40px;
    font-size:1.2rem;
}

.error{
    text-align:center;
    color:red;
    grid-column:1/-1;
    padding:40px;
}

footer{
    text-align:center;
    padding:30px;
    color:#777;
}

</style>
</head>
<body>

<header>
    <h1>MediaLab Åbningstider</h1>

    <p>
        Åbningstiderne opdateres løbende. <br>
        Der kan være lukket grundet manglende bemanding.
    </p>

    <div class="top-links">
        <a href="https://equipment.hum.aau.dk/"
           target="_blank"
           rel="noopener noreferrer">
           Booking
        </a>

        <a href="https://www.moodle.aau.dk/course/view.php?id=59946"
           target="_blank"
           rel="noopener noreferrer">
           Vores Moodleside
        </a>
    </div>
</header>

<div class="container">
    <div class="schedule" id="schedule">
        <div class="loading">
            Indlæser data...
        </div>
    </div>
</div>

<footer>
	<h3>Hvis siden ikke er opdateret, eller vi har lukket kan i sende os en mail og vi vil svare hurtigst muligt.</h3> <br>
	<br>
    Kontakt: <br> 
	Adresse: Rendsburggade 14, 9000 Aalborg, Lokale: 2.252 <br>
	Mail: medialab@ikk.aau.dk 
</footer>

<script>

// INDSÆT DIN APPS SCRIPT URL HER
const API_URL = "https://script.google.com/macros/s/AKfycbxF8B7zx0ziHucNMt3_ChBXAEdp0kk-Z3a2FHgZMONPuLQk56cH39gRRHGl2jNHFOV4XA/exec";

const schedule = document.getElementById("schedule");

const today = new Date();
today.setHours(0,0,0,0);

fetch(API_URL)
.then(response => response.json())
.then(data => {

    schedule.innerHTML = "";

    data.forEach(row => {

        const card = document.createElement("div");
        card.classList.add("card");

        let isToday = false;

        if(row.date){

            const rowDate = new Date(row.date);

            if(!isNaN(rowDate)){
                rowDate.setHours(0,0,0,0);

                if(rowDate.getTime() === today.getTime()){
                    isToday = true;
                    card.classList.add("today");
                }
            }
        }

        let html = "";

        if(isToday){
            html += `<div class="today-badge">I DAG</div>`;
        }

        let formattedDate = "";

if(row.date){

    const d = new Date(row.date);

    const day = String(d.getDate()).padStart(2,"0");
    const month = String(d.getMonth() + 1).padStart(2,"0");
    const year = d.getFullYear();

    formattedDate = `${day}-${month}-${year}`;
}

html += `
    <div class="day">${row.day}</div>
    <div class="date">${formattedDate}</div>
`;

        if(row.person && row.time){

            html += `
                <div class="person">
                    <span class="status-dot green"></span>
                    ${row.person}
                </div>

                <div class="time">
                    ${row.time}
                </div>
            `;

        } else {

            html += `
                <div class="closed">
                    <span class="status-dot red"></span>
                    Lukket
                </div>
            `;

        }

        card.innerHTML = html;
        schedule.appendChild(card);

    });

})
.catch(error => {

    console.error(error);

    schedule.innerHTML = `
        <div class="error">
            Kunne ikke hente data fra Google Sheets
        </div>
    `;
});

</script>

</body>
</html>
