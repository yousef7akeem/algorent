<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Algorent Application</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            margin: 0;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 900px;
            margin: 0 auto;
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        h1 {
            text-align: center;
            color: #333;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input[type="text"],
        input[type="password"],
        select {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        .file-upload {
            margin-top: 5px;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #45a049;
        }
        .image-preview {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            margin-top: 10px;
        }
        .image-preview img {
            max-width: 100px;
            max-height: 100px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        .hidden {
            display: none;
        }
        .login-form {
            max-width: 400px;
            margin: 0 auto;
        }
        .admin-controls {
            margin-top: 20px;
        }
        #adminContent {
            margin-top: 30px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        table, th, td {
            border: 1px solid #ddd;
        }
        th, td {
            padding: 12px;
            text-align: left;
        }
        th {
            background-color: #f2f2f2;
        }
        tr:nth-child(even) {
            background-color: #f9f9f9;
        }
        .logout-btn {
            background-color: #f44336;
            float: right;
        }
        .logout-btn:hover {
            background-color: #d32f2f;
        }
        .view-images {
            cursor: pointer;
            color: blue;
            text-decoration: underline;
        }
        .modal {
            display: none;
            position: fixed;
            z-index: 1;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0,0,0,0.8);
        }
        .modal-content {
            background-color: #fefefe;
            margin: 5% auto;
            padding: 20px;
            border: 1px solid #888;
            width: 80%;
            max-width: 800px;
            border-radius: 5px;
        }
        .close {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
            cursor: pointer;
        }
        .close:hover {
            color: black;
        }
        .image-gallery {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
        }
        .image-gallery img {
            max-width: 200px;
            max-height: 200px;
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Login Section (shown when not authenticated) -->
        <div id="loginSection" class="hidden">
            <h1>Admin Login</h1>
            <div class="login-form">
                <div class="form-group">
                    <label for="adminUsername">Username:</label>
                    <input type="text" id="adminUsername" required>
                </div>
                <div class="form-group">
                    <label for="adminPassword">Password:</label>
                    <input type="password" id="adminPassword" required>
                </div>
                <button id="loginBtn">Login</button>
                <button id="showRegisterBtn">Go to Registration</button>
            </div>
        </div>

        <!-- Registration Section -->
        <div id="registrationSection">
            <h1>Algorent Registration</h1>
            <form id="registrationForm" enctype="multipart/form-data">
                <div class="form-group">
                    <label for="username">Full Name:</label>
                    <input type="text" id="username" name="username" required>
                </div>
                <div class="form-group">
                    <label for="userId">National ID:</label>
                    <input type="text" id="userId" name="userId" required>
                </div>
                <div class="form-group">
                    <label for="userType">User Type:</label>
                    <select id="userType" name="userType" required>
                        <option value="">Select Type</option>
                        <option value="apartment">Apartment Owner</option>
                        <option value="shop">Shop Owner</option>
                        <option value="fitter">Fitter</option>
                    </select>
                </div>

                <!-- Dynamic file upload fields based on user type -->
                <div id="fileUploadFields">
                    <!-- Fields will be populated by JavaScript -->
                </div>

                <button type="submit">Submit Registration</button>
                <button type="button" id="showAdminLoginBtn">Admin Login</button>
            </form>
        </div>

        <!-- Admin Section (hidden by default) -->
        <div id="adminSection" class="hidden">
            <h1>Admin Dashboard <button class="logout-btn" id="logoutBtn">Logout</button></h1>
            
            <div id="adminContent">
                <h2>Registered Users</h2>
                <div id="userTableContainer">
                    <table id="userTable">
                        <thead>
                            <tr>
                                <th>ID</th>
                                <th>Name</th>
                                <th>National ID</th>
                                <th>Type</th>
                                <th>Registration Date</th>
                                <th>Actions</th>
                            </tr>
                        </thead>
                        <tbody id="userTableBody">
                            <!-- User data will be loaded here -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal for viewing images -->
    <div id="imageModal" class="modal">
        <div class="modal-content">
            <span class="close">&times;</span>
            <h2 id="modalTitle">User Images</h2>
            <div class="image-gallery" id="modalImageGallery">
                <!-- Images will be loaded here -->
            </div>
        </div>
    </div>

    <script>
        // DOM elements
        const loginSection = document.getElementById('loginSection');
        const registrationSection = document.getElementById('registrationSection');
        const adminSection = document.getElementById('adminSection');
        const fileUploadFields = document.getElementById('fileUploadFields');
        const userTypeSelect = document.getElementById('userType');
        const registrationForm = document.getElementById('registrationForm');
        const showAdminLoginBtn = document.getElementById('showAdminLoginBtn');
        const showRegisterBtn = document.getElementById('showRegisterBtn');
        const loginBtn = document.getElementById('loginBtn');
        const logoutBtn = document.getElementById('logoutBtn');
        const adminUsername = document.getElementById('adminUsername');
        const adminPassword = document.getElementById('adminPassword');
        const userTableBody = document.getElementById('userTableBody');
        const imageModal = document.getElementById('imageModal');
        const modalImageGallery = document.getElementById('modalImageGallery');
        const closeModal = document.querySelector('.close');

        // Sample database (in a real app, this would be server-side)
        let users = JSON.parse(localStorage.getItem('algorent_users')) || [];
        let currentUser = null;

        // Initialize the page
        function init() {
            // Check if admin is already logged in
            const isAdmin = localStorage.getItem('algorent_adminLoggedIn') === 'true';
            if (isAdmin) {
                showAdminSection();
            } else {
                showRegistrationSection();
            }

            // Set up event listeners
            userTypeSelect.addEventListener('change', updateFileUploadFields);
            showAdminLoginBtn.addEventListener('click', showLoginSection);
            showRegisterBtn.addEventListener('click', showRegistrationSection);
            loginBtn.addEventListener('click', handleAdminLogin);
            logoutBtn.addEventListener('click', handleAdminLogout);
            registrationForm.addEventListener('submit', handleRegistrationSubmit);
            closeModal.addEventListener('click', () => imageModal.style.display = 'none');
            window.addEventListener('click', (event) => {
                if (event.target === imageModal) {
                    imageModal.style.display = 'none';
                }
            });

            // Initialize file upload fields
            updateFileUploadFields();
        }

        // Show/hide sections
        function showLoginSection() {
            loginSection.classList.remove('hidden');
            registrationSection.classList.add('hidden');
            adminSection.classList.add('hidden');
        }

        function showRegistrationSection() {
            loginSection.classList.add('hidden');
            registrationSection.classList.remove('hidden');
            adminSection.classList.add('hidden');
        }

        function showAdminSection() {
            loginSection.classList.add('hidden');
            registrationSection.classList.add('hidden');
            adminSection.classList.remove('hidden');
            loadUserData();
        }

        // Update file upload fields based on user type
        function updateFileUploadFields() {
            const userType = userTypeSelect.value;
            fileUploadFields.innerHTML = '';

            if (!userType) return;

            if (userType === 'apartment') {
                addFileUploadField('proofOfOwnership', 'Proof of Ownership (Required)');
                addFileUploadField('nationalId', 'Copy of National ID Card (Required)');
                addFileUploadField('receipt', 'Receipt (Required)');
                addFileUploadField('apartmentPhotos', 'Photos of the Apartment (Required)');
            } else if (userType === 'shop') {
                addFileUploadField('proofOfOwnership', 'Proof of Ownership (Required)');
                addFileUploadField('nationalId', 'Copy of National ID Card (Required)');
                addFileUploadField('productImages', 'Product Images (Required)');
            } else if (userType === 'fitter') {
                addFileUploadField('nationalId', 'Copy of National ID Card (Required)');
                addFileUploadField('certificate', 'Certificate (Required)');
            }
        }

        function addFileUploadField(name, label) {
            const div = document.createElement('div');
            div.className = 'form-group';
            
            const labelEl = document.createElement('label');
            labelEl.textContent = label;
            
            const input = document.createElement('input');
            input.type = 'file';
            input.className = 'file-upload';
            input.name = name;
            input.required = true;
            input.multiple = name === 'apartmentPhotos' || name === 'productImages';
            input.accept = 'image/*';
            
            const preview = document.createElement('div');
            preview.className = 'image-preview';
            preview.id = `${name}Preview`;
            
            input.addEventListener('change', function() {
                updateImagePreview(this, preview.id);
            });
            
            div.appendChild(labelEl);
            div.appendChild(input);
            div.appendChild(preview);
            fileUploadFields.appendChild(div);
        }

        function updateImagePreview(input, previewId) {
            const preview = document.getElementById(previewId);
            preview.innerHTML = '';
            
            if (input.files) {
                for (let i = 0; i < input.files.length; i++) {
                    const reader = new FileReader();
                    reader.onload = function(e) {
                        const img = document.createElement('img');
                        img.src = e.target.result;
                        preview.appendChild(img);
                    }
                    reader.readAsDataURL(input.files[i]);
                }
            }
        }

        // Handle registration form submission
        function handleRegistrationSubmit(e) {
            e.preventDefault();
            
            const formData = {
                username: document.getElementById('username').value,
                userId: document.getElementById('userId').value,
                userType: userTypeSelect.value,
                registrationDate: new Date().toISOString(),
                images: {}
            };
            
            // Collect file names (in a real app, you would upload the files)
            const fileInputs = registrationForm.querySelectorAll('input[type="file"]');
            fileInputs.forEach(input => {
                if (input.files.length > 0) {
                    formData.images[input.name] = [];
                    for (let i = 0; i < input.files.length; i++) {
                        formData.images[input.name].push({
                            name: input.files[i].name,
                            data: URL.createObjectURL(input.files[i]) // In real app, upload to server
                        });
                    }
                }
            });
            
            // Save to "database"
            users.push(formData);
            localStorage.setItem('algorent_users', JSON.stringify(users));
            
            alert('Registration submitted successfully!');
            registrationForm.reset();
            fileUploadFields.innerHTML = '';
        }

        // Admin functions
        function handleAdminLogin() {
            if (adminUsername.value === 'yousef' && adminPassword.value === '123') {
                localStorage.setItem('algorent_adminLoggedIn', 'true');
                showAdminSection();
            } else {
                alert('Invalid username or password');
            }
        }

        function handleAdminLogout() {
            localStorage.removeItem('algorent_adminLoggedIn');
            showLoginSection();
            adminUsername.value = '';
            adminPassword.value = '';
        }

        function loadUserData() {
            userTableBody.innerHTML = '';
            
            users.forEach((user, index) => {
                const row = document.createElement('tr');
                
                row.innerHTML = `
                    <td>${index + 1}</td>
                    <td>${user.username}</td>
                    <td>${user.userId}</td>
                    <td>${getUserTypeLabel(user.userType)}</td>
                    <td>${new Date(user.registrationDate).toLocaleString()}</td>
                    <td><span class="view-images" data-index="${index}">View Images</span></td>
                `;
                
                userTableBody.appendChild(row);
            });
            
            // Add event listeners to view image buttons
            document.querySelectorAll('.view-images').forEach(button => {
                button.addEventListener('click', function() {
                    const index = parseInt(this.getAttribute('data-index'));
                    showUserImages(index);
                });
            });
        }

        function getUserTypeLabel(type) {
            switch(type) {
                case 'apartment': return 'Apartment Owner';
                case 'shop': return 'Shop Owner';
                case 'fitter': return 'Fitter';
                default: return type;
            }
        }

        function showUserImages(index) {
            const user = users[index];
            modalImageGallery.innerHTML = '';
            document.getElementById('modalTitle').textContent = `Images for ${user.username}`;
            
            for (const [key, images] of Object.entries(user.images)) {
                images.forEach(image => {
                    const imgContainer = document.createElement('div');
                    imgContainer.innerHTML = `
                        <div>
                            <p><strong>${key}:</strong> ${image.name}</p>
                            <img src="${image.data}" alt="${image.name}">
                        </div>
                    `;
                    modalImageGallery.appendChild(imgContainer);
                });
            }
            
            imageModal.style.display = 'block';
        }

        // Initialize the application
        init();
    </script>
</body>
</html>
