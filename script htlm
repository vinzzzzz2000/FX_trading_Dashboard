<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@emailjs/browser@3/dist/email.min.js"></script>
    "></script> <!-- Email.js -->
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #121212;
            color: #ffffff;
            margin: 0;
            padding: 10px;
        }
        h1 {
            color: #fc13ab;
        }
        .logo {
            height: 80px; /* Adjust the size of the logo */
            margin-right: 5px;
        }
        .tabs {
            display: flex;
            background-color: #333;
            border-bottom: 2px solid #444;
        }
        .tab {
            flex: 1;
            padding: 15px;
            text-align: center;
            cursor: pointer;
            font-weight: bold;
            font-size: 1rem;
            color: #ecf408;
        }
        .tab.active {
            background-color: #555;
        }
        .content {
            display: none;
            padding: 10px;
        }
        .content.active {
            display: block;
        }
        #candlestick-chart {
            height: 800px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        table th, table td {
            border: 1px solid #0c0c00;
            padding: 4px;
            text-align: center;
        }
        table th {
            background-color: #555;
        }
        #hourlyRateChart {
            height: 700px;
        }
    </style>
</head>
<body>
    <h1>
        <img src="C:\Users\ext.vincent.donnat\Downloads\new_Logo.png" alt="Logo" class="logo"> <!-- Replace with your uploaded image URL -->
        FX Trading Dashboard
    </h1>
    <div class="tabs">
        <div class="tab active" data-target="live-rate">Live Rate</div>
        <div class="tab" data-target="candlestick-chart-tab">Candlestick Chart</div>
        <div class="tab" data-target="upload-mt300-tab">Upload MT300</div>
        <div class="tab" data-target="optimum-trading-hour">Optimum Trading Hour</div>
        <div class="tab" data-target="alerts-tab">Alerts</div>
    </div>

    <!-- Live Rate Tab -->
    <div class="content active" id="live-rate">
        <h2>Live EUR/USD Rate</h2>
        <table id="live-rate-table"></table>
    </div>

    <!-- Candlestick Chart Tab -->
    <div class="content" id="candlestick-chart-tab">
         <div id="candlestick-chart"></div>
    </div>

    <!-- Upload MT300 Tab -->
    <div class="content" id="upload-mt300-tab">
        <h2>Upload MT300</h2>
        <input type="file" id="mt300-upload" accept=".txt">
        <button onclick="processMT300()">Process MT300</button>
        <h3>Comparison Results</h3>
        <table id="comparison-table">
            <thead>
                <tr>
                    <th>Timestamp</th>
                    <th>MT300 Rate</th>
                    <th>Stored Rate</th>
                    <th>Spread</th>
                    <th>P&L</th>
                </tr>
            </thead>
            <tbody></tbody>
        </table>
    </div>
    
    <!-- Optimum Trading Hour Tab -->
    <div id="optimum-trading-hour" class="content">
    <div id="hourlyRateChart"></div>
    </div>

     <!-- Alerts Tab -->
     <div class="content" id="alerts-tab">
        <h2>Set Alert</h2>
        <form id="alert-form">
            <label for="threshold">Threshold:</label>
            <input type="number" id="threshold" step="0.000001" placeholder="Enter threshold rate" required>
            <br>
            <label for="condition">Condition:</label>
            <select id="condition" required>
                <option value="above">Above</option>
                <option value="below">Below</option>
            </select>
            <br>
            <button type="button" onclick="setAlert()">Set Alert</button>
        </form>
        <p id="alert-status"></p>
    </div>

    <script>
        const liveRateApiUrl = "https://script.google.com/macros/s/AKfycbzzTmVFiumlrE6fAk6JI3qF1NNRA67mTSaEr1DLhjPiIskrgpmzrFuM9wIRlj3W9M2qjQ/exec";
        const candlestickApiUrl = "https://script.google.com/macros/s/AKfycbycCtxaE5MMkqDQebXKATjFOooESVu_DoRRJqHJ9gXM-ArSFoddfruHjiDz_8p9IwFudQ/exec";
        const comparisonApiUrl = "https://script.google.com/macros/s/AKfycbz7Xcdk6D2DN7iIZB915DVFn0_1bfFqMK0qYwLz_0o1xfkbB-A8EkemDJHlTugzwHPI9w/exec";
        const apiUrl = "https://script.google.com/macros/s/AKfycbyLXqKRmjD8PoQQQkGiyTGMx_Ls5J2rT3CfOLJqcE9zQkeVtmPqgLHz6qiKvDXdNk4HDw/exec";

        
    // Alert variables
       let alertSet = false; // Tracks whether an alert has been set
       let alertThreshold = 0; // Stores the threshold value
       let alertCondition = ""; // Stores the condition ('above' or 'below')
       let alertSent = false; // Tracks whether the email has already been sent    
    
        
    async function fetchLiveRate() {
    const table = document.getElementById("live-rate-table");
    table.innerHTML = `<tr><th>Loading rates...</th></tr>`;
    try {
        const response = await fetch(liveRateApiUrl);
        if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
        const apiData = await response.json();

        // Adjust timestamps and sort the data
        const adjustedAndSortedData = apiData.Data.map(row => {
            const adjustedTimestamp = new Date(row.Timestamp);
            adjustedTimestamp.setHours(adjustedTimestamp.getHours() + 1); // Add 1 hour to timestamp
            return { ...row, Timestamp: adjustedTimestamp.toISOString().replace('T', ' ').slice(0, 19) };
        }).sort((a, b) => new Date(b.Timestamp) - new Date(a.Timestamp)); // Sort by descending timestamp

        table.innerHTML = `
            <thead>
                <tr><th>Timestamp</th><th>Rate</th></tr>
            </thead>
            <tbody>
                ${adjustedAndSortedData.map(row => `<tr><td>${row.Timestamp}</td><td>${row.Rate.toFixed(6)}</td></tr>`).join("")}
            </tbody>
        `;

        // Check alert conditions if alert is set
        if (alertSet && !alertSent) {
            const latestRate = adjustedAndSortedData[0]?.Rate;
            if (
                (alertCondition === "above" && latestRate > alertThreshold) ||
                (alertCondition === "below" && latestRate < alertThreshold)
            ) {
                sendAlert(latestRate);
                alertSent = true; // Prevent multiple emails for the same alert
            }
        }
    } catch (error) {
        console.error("Error fetching live rates:", error);
        table.innerHTML = `<tr><th>Error loading rates.</th></tr>`;
    }
}

        async function fetchCandlestickData() {
            try {
                const response = await fetch(candlestickApiUrl);
                if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
                const apiData = await response.json();
                return apiData.Data;
            } catch (error) {
                console.error("Error fetching candlestick data:", error);
                return [];
            }
        }

        async function renderCandlestickChart() {
    const data = await fetchCandlestickData();
    if (data.length > 0) {
        // Adjust timestamps on X-axis
        const timestamps = data.map(entry => {
            const adjustedTimestamp = new Date(entry.Timestamp);
            adjustedTimestamp.setHours(adjustedTimestamp.getHours() + 1); // Add 1 hour
            return adjustedTimestamp.toISOString().replace('T', ' ').slice(0, 19);
        });

        const rates = data.map(entry => entry.Rate);

        const trace = {
            x: timestamps,
            open: rates.slice(0, -1),
            high: rates,
            low: rates,
            close: rates.slice(1),
            type: 'candlestick',
            increasing: { line: { color: '#00FF00' } },
            decreasing: { line: { color: '#FF0000' } }
        };

        const layout = {
            title: 'EUR/USD Candlestick Chart',
            xaxis: { title: 'Timestamp' },
            yaxis: { title: 'Price' },
            plot_bgcolor: "#121212",
            paper_bgcolor: "#121212",
            font: { color: "#ffffff" }
        };

        Plotly.newPlot('candlestick-chart', [trace], layout);
    }
}

        async function fetchStoredRates() {
            try {
                const response = await fetch(comparisonApiUrl);
                if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
                const apiData = await response.json();
                return apiData.Data;
            } catch (error) {
                console.error("Error fetching stored rates:", error);
                return [];
            }
        }

        async function processMT300() {
            const fileInput = document.getElementById("mt300-upload");
            const file = fileInput.files[0];
            if (!file) {
                alert("Please upload an MT300 file.");
                return;
            }

            const reader = new FileReader();
            reader.onload = async function() {
                const text = reader.result;
                const mt300Rates = parseMT300(text);
                const storedRates = await fetchStoredRates();
                displayComparison(mt300Rates, storedRates);
            };
            reader.readAsText(file);
        }

        function parseMT300(text) {
    const lines = text.split("\n");
    const rates = [];
    let timestamp = null;
    let rate = null;
    let amount = null;

    for (const line of lines) {
        // Extract timestamp from :30T:
        if (line.startsWith(":30T:")) {
            const rawTimestamp = line.split(":30T:")[1].trim();
            if (/^\d{8}$/.test(rawTimestamp)) {
                timestamp = `${rawTimestamp.slice(0, 4)}-${rawTimestamp.slice(4, 6)}-${rawTimestamp.slice(6, 8)}`;
            }
        }

        // Extract rate from :36:
        if (line.startsWith(":36:")) {
            rate = parseFloat(line.split(":36:")[1].replace(",", ".").trim());
        }

        // Extract amount from :32B:
        if (line.startsWith(":32B:")) {
            const rawAmount = line.split(":32B:")[1].trim();
            amount = parseFloat(rawAmount.replace(/[^\d.]/g, "")); // Remove currency codes like "USD"
        }

        // If all fields are present, add the entry to the results
        if (timestamp && rate && amount) {
            rates.push({ Timestamp: timestamp, Rate: rate, Amount: amount });
            timestamp = null; // Reset for the next entry
            rate = null;
            amount = null;
        }
    }

    return rates;
}

async function renderHourlyRateChart() {
  const apiUrl = "https://script.google.com/macros/s/AKfycbyLXqKRmjD8PoQQQkGiyTGMx_Ls5J2rT3CfOLJqcE9zQkeVtmPqgLHz6qiKvDXdNk4HDw/exec"; // Replace with your actual URL
  try {
    const response = await fetch(apiUrl);
    if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
    const data = await response.json();

    const labels = data.hours || [];
    const differences = data.differences || [];

    // Render the Plotly chart
    const trace = {
      x: labels,
      y: differences,
      type: 'bar',
      marker: { color: 'rgba(54, 162, 235, 0.7)' },
    };

    const layout = {
      title: 'Hourly Average vs Daily Average Rate Differences',
      xaxis: { title: 'Hour of the Day (24h)' },
      yaxis: { title: 'Difference From Daily Average Rate' },
      plot_bgcolor: "#121212",
      paper_bgcolor: "#121212",
      font: { color: "#ffffff" },
    };

    Plotly.newPlot('hourlyRateChart', [trace], layout);
  } catch (error) {
    console.error("Error rendering chart:", error);
  }
}

document.querySelectorAll(".tab").forEach((tab) => {
  tab.addEventListener("click", () => {
    document.querySelectorAll(".tab").forEach((t) => t.classList.remove("active"));
    document.querySelectorAll(".content").forEach((c) => c.classList.remove("active"));
    tab.classList.add("active");
    document.getElementById(tab.dataset.target).classList.add("active");

    if (tab.dataset.target === "optimum-trading-hour") {
      renderHourlyRateChart();
    }
  });
});

if (document.querySelector(".tab.active").dataset.target === "optimum-trading-hour") {
  renderHourlyRateChart();
}
  
        function displayComparison(mt300Rates, storedRates) {
            const tableBody = document.querySelector("#comparison-table tbody");
            tableBody.innerHTML = mt300Rates.map(mt300Rate => {
                const storedRate = storedRates.find(rate => rate.Timestamp.startsWith(mt300Rate.Timestamp));
                const spread = storedRate ? (mt300Rate.Rate - storedRate.Rate).toFixed(6) : "N/A";
                const pnl = storedRate
                    ? ((mt300Rate.Amount * mt300Rate.Rate) - (mt300Rate.Amount * storedRate.Rate)).toFixed(2)
                    : "N/A";
                return `
                    <tr>
                        <td>${mt300Rate.Timestamp}</td>
                        <td>${mt300Rate.Rate.toFixed(6)}</td>
                        <td>${storedRate ? storedRate.Rate.toFixed(6) : "N/A"}</td>
                        <td>${spread}</td>
                        <td>${pnl}</td>
                    </tr>
                `;
            }).join("");
        }

    function setAlert() {
    alertThreshold = parseFloat(document.getElementById("threshold").value);
    alertCondition = document.getElementById("condition").value;
    if (isNaN(alertThreshold)) {
        alert("Please enter a valid threshold.");
        return;
    }
    alertSet = true;
    alertSent = false; // Reset alert sent flag
    document.getElementById("alert-status").innerText = `Alert set: ${alertCondition} ${alertThreshold}`;
}

function sendAlert(currentRate) {
    emailjs.init("iXPR1LLih80QGitzU"); // Replace with your actual public key
    emailjs.send("service_g3ur3t5", "template_xnghwd4", {
        email: "vincent.donnat.fr@gmail.com", // Replace with the recipient email
        message: `Alert triggered! EUR/USD rate is ${currentRate.toFixed(6)} (Condition: ${alertCondition} ${alertThreshold}).`
    }).then(() => {
        document.getElementById("alert-status").innerText = `Alert sent to vincent.donnat.fr@gmail.com`;
    }).catch(error => {
        console.error("Error sending email:", error);
        document.getElementById("alert-status").innerText = `Error sending alert: ${error.text || error.message}`;
    });
}


        document.querySelectorAll(".tab").forEach(tab => {
            tab.addEventListener("click", () => {
                document.querySelectorAll(".tab").forEach(t => t.classList.remove("active"));
                document.querySelectorAll(".content").forEach(c => c.classList.remove("active"));
                tab.classList.add("active");
                document.getElementById(tab.dataset.target).classList.add("active");
            });
        });

        fetchLiveRate();
        setInterval(fetchLiveRate, 10000);

        renderCandlestickChart();
        setInterval(renderCandlestickChart, 60000);
    </script>
</body>
</html>
