<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dynamic QR Code Example</title>
    <script src="https://cdn.jsdelivr.net/npm/qrcode"></script>
</head>
<body>
    <h1>Customer Points Balance</h1>
    <div id="qrcode"></div>

    <script>
        // Example customer data (in practice, this would come from your backend)
        const customerData = {
            name: "John Doe",
            pointsBalance: 150,
            lastTransaction: "2024-09-03"
        };

        // Function to generate QR code
        function generateQRCode(data) {
            const qrCodeContainer = document.getElementById('qrcode');
            qrCodeContainer.innerHTML = ""; // Clear previous QR code
            QRCode.toCanvas(qrCodeContainer, JSON.stringify(data), function (error) {
                if (error) console.error(error);
                console.log('QR code generated!');
            });
        }

        // Generate QR code with customer data
        generateQRCode(customerData);
    </script>
</body>
</html>
