<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Digital Visiting Card</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: Arial, sans-serif;
    }

    body {
      line-height: 1.6;
      color: #333;
      background: #f4f4f4;
      padding: 20px;
    }

    .container {
      max-width: 600px;
      margin: 0 auto;
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    h1 {
      text-align: center;
      color: #2c3e50;
      margin-bottom: 20px;
    }

    .form-group {
      margin-bottom: 15px;
    }

    label {
      display: block;
      font-weight: bold;
      margin-bottom: 5px;
    }

    input, textarea {
      width: 100%;
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 5px;
      font-size: 16px;
    }

    #profile-pic {
      max-width: 100%;
      height: auto;
      display: block;
      margin: 10px auto;
      border-radius: 50%;
    }

    button {
      display: block;
      width: 100%;
      padding: 10px;
      background: #3498db;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      font-size: 16px;
      margin: 10px 0;
    }

    button:hover {
      background: #2980b9;
    }

    #qr-code {
      text-align: center;
      margin: 20px 0;
    }

    .share-buttons {
      display: flex;
      gap: 10px;
      justify-content: center;
    }

    .share-buttons button {
      flex: 1;
      background: #2c3e50;
    }

    .share-buttons button:hover {
      background: #34495e;
    }

    @media (max-width: 480px) {
      .container {
        padding: 15px;
      }

      h1 {
        font-size: 1.5rem;
      }

      button {
        font-size: 14px;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Create Your Digital Visiting Card</h1>
    <form id="card-form">
      <div class="form-group">
        <label for="name">Full Name</label>
        <input type="text" id="name" placeholder="Enter your name" required>
      </div>
      <div class="form-group">
        <label for="location">Current Location</label>
        <input type="text" id="location" placeholder="Enter your location" required>
      </div>
      <div class="form-group">
        <label for="profession">Profession</label>
        <input type="text" id="profession" placeholder="Enter your profession" required>
      </div>
      <div class="form-group">
        <label for="email">Email</label>
        <input type="email" id="email" placeholder="Enter your email">
      </div>
      <div class="form-group">
        <label for="phone">Phone Number</label>
        <input type="tel" id="phone" placeholder="Enter your phone number">
      </div>
      <div class="form-group">
        <label for="website">Website (Optional)</label>
        <input type="url" id="website" placeholder="Enter your website URL">
      </div>
      <div class="form-group">
        <label for="bio">Bio (Optional)</label>
        <textarea id="bio" placeholder="Enter a short bio" rows="4"></textarea>
      </div>
      <div class="form-group">
        <label for="profile-pic-upload">Profile Picture</label>
        <input type="file" id="profile-pic-upload" accept="image/*">
        <img id="profile-pic" src="" alt="Profile Picture" style="display: none;">
      </div>
      <button type="submit">Generate QR Code</button>
    </form>
    <div id="qr-code"></div>
    <div class="share-buttons">
      <button onclick="shareViaWhatsApp()">Share via WhatsApp</button>
      <button onclick="shareViaInstagram()">Share via Instagram</button>
      <button onclick="shareViaFacebook()">Share via Facebook</button>
    </div>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
  <script>
    const form = document.getElementById('card-form');
    const qrCodeDiv = document.getElementById('qr-code');
    let vCardData = '';

    // Handle image upload and display
    document.getElementById('profile-pic-upload').addEventListener('change', function(e) {
      const file = e.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function(e) {
          const img = document.getElementById('profile-pic');
          img.src = e.target.result;
          img.style.display = 'block';
        };
        reader.readAsDataURL(file);
      }
    });

    // Generate vCard and QR code
    form.addEventListener('submit', function(e) {
      e.preventDefault();
      const name = document.getElementById('name').value;
      const location = document.getElementById('location').value;
      const profession = document.getElementById('profession').value;
      const email = document.getElementById('email').value;
      const phone = document.getElementById('phone').value;
      const website = document.getElementById('website').value;
      const bio = document.getElementById('bio').value;

      // Create vCard data
      vCardData = `BEGIN:VCARD
VERSION:3.0
FN:${name}
TITLE:${profession}
ADR;TYPE=WORK:${location}
EMAIL:${email}
TEL:${phone}
URL:${website}
NOTE:${bio}
END:VCARD`;

      // Clear previous QR code
      qrCodeDiv.innerHTML = '';

      // Generate QR code
      new QRCode(qrCodeDiv, {
        text: vCardData,
        width: 200,
        height: 200,
        colorDark: '#000000',
        colorLight: '#ffffff',
        correctLevel: QRCode.CorrectLevel.H
      });
    });

    // Share via WhatsApp
    function shareViaWhatsApp() {
      if (!vCardData) {
        alert('Please generate a QR code first!');
        return;
      }
      const encodedData = encodeURIComponent(vCardData);
      const vCardBlob = new Blob([vCardData], { type: 'text/vcard' });
      const vCardUrl = URL.createObjectURL(vCardBlob);
      const text = `Check out my digital visiting card! Download it here: ${vCardUrl}`;
      const whatsappUrl = `https://wa.me/?text=${encodeURIComponent(text)}`;
      window.open(whatsappUrl, '_blank');
    }

    // Share via Instagram (using Web Share API or fallback link)
    function shareViaInstagram() {
      if (!vCardData) {
        alert('Please generate a QR code first!');
        return;
      }
      const text = 'Check out my digital visiting card!';
      if (navigator.share) {
        navigator.share({
          title: 'My Digital Visiting Card',
          text: text,
          url: window.location.href
        }).catch(err => console.error('Share failed:', err));
      } else {
        alert('Instagram sharing is limited. Please share the QR code image manually on Instagram.');
      }
    }

    // Share via Facebook
    function shareViaFacebook() {
      if (!vCardData) {
        alert('Please generate a QR code first!');
        return;
      }
      const encodedData = encodeURIComponent(vCardData);
      const vCardBlob = new Blob([vCardData], { type: 'text/vcard' });
      const vCardUrl = URL.createObjectURL(vCardBlob);
      const facebookUrl = `https://www.facebook.com/sharer/sharer.php?u=${encodeURIComponent(vCardUrl)}&quote=${encodeURIComponent('Check out my digital visiting card!')}`;
      window.open(facebookUrl, '_blank');
    }
  </script>
</body>
</html>
