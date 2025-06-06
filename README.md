# 🛍️ CommerceAPI 

A robust and scalable backend API for an e-commerce platform, built with **Node.js**, **Express.js**, and **MongoDB**. This application supports full CRUD operations for products, categories, and orders, along with secure **JWT-based user authentication**. Designed with modular architecture and tested thoroughly using **Postman**, it's an ideal foundation for modern online shopping applications.

---
## 🔧 Tech Stack

- **Node.js** & **Express.js** – Server-side runtime and framework

- **MongoDB** & **Mongoose** – NoSQL database and ODM

- **JWT Authentication** – Secure token-based auth system

- **RESTful API** – Standardized and scalable endpoints

- **Postman** – API testing and validation

---

## 🚀 Features

- 🛍️ Product Management (CRUD)
- 🗂️ Category & Subcategory System
- 🔐 User Authentication & Authorization (JWT)
- 📦 Order Processing Logic
- 🏷️ Brand Assignment & Filtering
- 📄 Environment-based Config (using `.env`)
- 💬 Clear error handling and status codes

---

## 📁 Project Structure

```
├── DB/            # Database connection setup and seed scripts
├── config/        # Environment variables and JWT configuration
├── src/           # Application logic: models, routes, and controllers
├── app.js         # Main Express application entry point
└── package.json   # Project metadata and dependencies
```



---
## 🧪 API Testing

Use **Postman** to interact with and test the API endpoints. Below are the key endpoints along with request methods, example payloads, expected responses, and authentication details where applicable.


---

## 📘 Complete API Usage Guide
### 1. Register a New User  
- **Endpoint:** `POST /api/v1/auth/signUp`  
- **Description:** Create a new user account.  
- **Request Body (JSON):**
```json
{
    "userName": "maryam",
    "email": "maryammohamedsobhy357@gmail.com",
    "password": "Test123",
    "cPassword": "Test123"

}
```

Successful Response (201 Created):
```json
{
    "message": "Registration successful. Please check your email to confirm your account.",
    "savedUser": {
        "userName": "maryam",
        "email": "maryammohamedsobhy357@gmail.com",
        "password": "$2a$09$rRMFNFsj3zwX.PoqLCJLHO0T25qGZpCD6kwExEcd6tBa8ES1Ozqy6",
        "role": "User",
        "active": false,
        "confirmEmail": false,
       ...
    }
}


```
- Note: The user account is created but inactive until the email is confirmed.

#### ⚠️ Edge Case:
**Email Already Registered but Not Confirmed**
If a user tries to register again using the same email without confirming it first, the API responds the following Response:

Response (409 Conflict):

```json
{
  "message": "This email is already registered but not yet confirmed. Please check your inbox to confirm your email."
}
```
#### 🔗 Email Confirmation Flow

After a user successfully registers, a **confirmation email** is sent to the provided email address.  
To activate the account, the user **must click the confirmation link** in the email.

> ℹ️ **Note:**
> - The account remains **inactive** until the email is confirmed.
> - Users **cannot log in or re-register** with the same email until confirmation is completed.

#### ✅ Validation Rules
The Sign Up endpoint uses Joi validation to ensure that user input meets specific criteria before processing. If validation fails, the API responds with a clear error message.
- Validation Schema:
 ```json
  {
  "userName": "string (required) - minimum 2, maximum 20 characters",
  "email": "string (required) - must be a valid email ending with .com or .net",
  "password": "string (required) - must match a specific pattern",
  "cPassword": "string (required) - must exactly match `password`"
}
```
🔐 Password Pattern Requirements:
- Must start with an uppercase letter
- Followed by 3 to 8 lowercase letters or digits
- Examples of valid passwords:
Aabc1
Zx12

❌ If the password doesn’t match the pattern:
You will receive an error message like:
```json
{
  "message": "Not matching pattern"
}
```

#### 📷 SignUp API
![SignUp Request & Response](./imgs/signup_req_res.png)

#### 📷 Email Confirmation for Signing Up
![Email Confirmation for Signing Up](./imgs/email_confirmation.png)

[📬 Click here to open the signUp request](https://www.postman.com/graduation-space-584306/commerceapi/request/5ugm73s/commerceapi?action=share&creator=21090382&ctx=documentation)


### 2. User Login
- **Endpoint:** `POST /api/v1/auth/login`  
- **Description:** Authenticate user and receive a JWT token for authorized requests.   
- **Request Body (JSON):**
```json
{ 
  "email": "maryammohamedsobhy357@gmail.com",
  "password": "Test123"
}
```
Successful Response (200 OK):

```json
{
    "message": "Welcome",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjY4MzA3Yzc2ZjIwMmJlMjE1NjI4NWYxNCIsImlzTG9nZ2VkSW4iOnRydWUsImlhdCI6MTc0ODAwOTg0NiwiZXhwIjoxNzQ4MTgyNjQ2fQ.8WpE8Z1MQWN9ArenhgzFQtfkfkPLr8mXbE_4G8LEqps"
}
```
⚠️ Important Note:
The user must confirm their email before logging in.
If the email is not confirmed, the login request will fail even with correct credentials.

💡 Save the returned JWT token. Use it as a Bearer token in the Authorization header for all protected endpoints.
Authorization: "Bearer__"+jwt_token

#### 📷Login API
![Login API](./imgs/login_req_res.png)

[📬 Click here to open the login request](https://www.postman.com/graduation-space-584306/commerceapi/request/ug5adby/commerceapi?action=share&creator=21090382&ctx=documentation)

### 3. Update Role 
- **Endpoint:** `PUT /api/v1/auth/updateRole`  
- **Description:** Promote a user to the Admin role after verifying email confirmation. Requires an authenticated Admin user.
- **Authentication:**: Bearer__<jwt_token>
- **Authorization:** Admin
- **Request Body (JSON):**
```json
{
    "userId":"6832feee9fcf13c4bc5d2d16"

}
```
Successful Response (200 OK):

```json
{
    "message": "Updated",
    "updatedUser": {
        "_id": "6832feee9fcf13c4bc5d2d16",
        "userName": "menna",
        "email": "menna@gmail.com",
        "password": "$2a$09$dLppfvL3r6yrIQTiyPDQGeEoMG2eA3qOORlY7MD9tdpiBwxvtTTk6",
        "role": "Admin",
        "active": false,
        "confirmEmail": true,
        ...
}
}
```
⚠️ Important Note:
Only users with the Admin role are authorized to promote other users to Admin. If a non-admin user attempts this operation, the request will be rejected.

Forbidden Response (403 Forbidden):
```json
{
    "message": "Not authorized user",
}
```

#### 📷UpdateRole API
![Update_Role API](./imgs/update_role_req_res.png)

[📬 Click here to open the update_role request](https://www.postman.com/graduation-space-584306/commerceapi/request/cqgqa9g/commerceapi?action=share&creator=21090382&ctx=documentation)


### 4. SendCode
- **Endpoint:** `POST /api/v1/auth/sendCode`  
- **Description:** Sends a one-time password (OTP) to the email address provided. This is used for password recovery and works only if the email belongs to a registered user.
- **Request Body (JSON):**
```json
{
    "email":"maryammohamedsobhy357@gmail.com"

}
```
Successful Response (200 OK):

```json
{
    "message": "Done, please check your email"
}
```

#### 📷 OTP Email
![OTP Email](./imgs/OTP_email.png)

#### 📷SendCode API
![SendCode API](./imgs/sendCode_req_res.png)

[📬 Click here to open the send_OTP_code request](https://www.postman.com/graduation-space-584306/commerceapi/request/z7uqe3k/commerceapi?action=share&creator=21090382&ctx=documentation)


### 5. ForgetPassword
- **Endpoint:** `POST /api/v1/auth/forgetPassword`  
- **Description:** Resets the user's password using an OTP code previously sent to their email. The request must include the user's email, the OTP code, and the new password. This only works if the provided email and OTP match an existing user.
- **Request Body (JSON):**
```json
{
    "OTPCode":"gCQdUzQ3NntJDtxrwx8K8",
    "email":"maryammohamedsobhy357@gmail.com",
    "password":"Newpass123"
    

}
```
Successful Response (200 OK):

```json
{
    "message": "Done, your password has been changed successfully"
}
```


[📬 Click here to open the forget_password_request](https://www.postman.com/graduation-space-584306/commerceapi/request/2wxpjcm/commerceapi?action=share&creator=21090382&ctx=documentation)



### 6. Add Brand
**Endpoint:** `POST /api/v1/brand/addBrand`  
**Description:** Creates a new brand by uploading an image to Cloudinary using **Multer** and saving the brand details (including image URL, slug, and creator) to the database. Requires an image file and authentication.
- **Authentication:**: Bearer__<jwt_token>
- **Authorization:** Admin
- **Request Body (Form-data):**
   - image (file): The image file for the brand.
   - name (text): The name of the brand.

![addBrand req body](./imgs/addBrand_request_body.png)
  
Successful Response (201 Created):
```json
    {
    "message": "Created",
    "result": {
        "name": "Apple",
        "image": "https://res.cloudinary.com/ds7wrpkx4/image/upload/v1748867386/brands/pbjvfxvtjn2ipgt93gar.jpg",
        "public_id": "brands/pbjvfxvtjn2ipgt93gar",
        "slug": "Apple",
        "createdBy": "683c4fad80b62da136c98c9d",
        "_id": "683d993b6414364a2e6f13e4",
        "createdAt": "2025-06-02T12:29:47.339Z",
        "updatedAt": "2025-06-02T12:29:47.339Z",
        "__v": 0
    }
}
```
![images_uploaded_to_cloudinar_API](./imgs/images_uploaded_to_cloudinary.png)
#### 📷addBrand API
![addBrand API](./imgs/addBrand_req_res.png)

[📬 Click here to open the_add_brand_request](https://www.postman.com/graduation-space-584306/commerceapi/request/oukxpp6/commerceapi?action=share&creator=21090382&ctx=documentation)


### 7. Update Brand
**Endpoint:** `PUT /api/v1/brand/updateBrand`  
**Description:** Updates a brand's information including its name and image. If a new image is uploaded, the old image is deleted from Cloudinary and replaced with the new one. The brand's name is also converted into a URL-friendly slug if provided.
- **Authentication:**: Bearer__<jwt_token>
- **Authorization:** Admin
- **Request Body (JSON):**
```json
    {
    "name":"brand name edited"
    }

```
  
Successful Response (200 OK):
```json
   {
    "message": "Updated",
    "Brand": {
        "_id": "683da1adddadfbcacc03354f",
        "name": "brand name edited",
        "image": "https://res.cloudinary.com/ds7wrpkx4/image/upload/v1748869548/brands/hvtvrvopudguthm552p4.jpg",
        "public_id": "brands/hvtvrvopudguthm552p4",
        "slug": "brand-name-edited",
        "createdBy": "683c4fad80b62da136c98c9d",
        "createdAt": "2025-06-02T13:05:49.072Z",
        "updatedAt": "2025-06-02T13:07:00.009Z",
        "__v": 0
    }
}
```
#### 📷UpdateBrand API
![UpdateBrand API](./imgs/update_brand_req_res.png)

[📬 Click here to open the_update_brand_request](https://www.postman.com/graduation-space-584306/commerceapi/request/iru1do8/commerceapi?action=share&creator=21090382&ctx=documentation)


### 8. Get All Brands
**Endpoint:** `GET /api/v1/brand/getAllBrands`  
**Description:** Retrieves a paginated list of all brand entries from the database. This API supports pagination through page and size query parameters.
- **Authentication:**: Bearer__<jwt_token>
- **Authorization:** Admin, User
  
Successful Response (200 OK):
```json
{
    "message": "Done",
    "allBrands": [
        {
            "_id": "683da1adddadfbcacc03354f",
            "name": "brand name edited",
            "image": "https://res.cloudinary.com/ds7wrpkx4/image/upload/v1748869548/brands/hvtvrvopudguthm552p4.jpg",
            "public_id": "brands/hvtvrvopudguthm552p4",
            "slug": "brand-name-edited",
            "createdBy": "683c4fad80b62da136c98c9d",
            "createdAt": "2025-06-02T13:05:49.072Z",
            "updatedAt": "2025-06-02T13:07:00.009Z",
            "__v": 0
        }
    ]
}
```
### Why I Used paginate(page, size)
✅** Performance Optimization**
When your database has hundreds or thousands of records (like many brands), it's inefficient to fetch them all at once. It slows down:
    - Your backend (heavy memory use)
    - Your frontend (long loading time)
    - Your user experience (poor UX)

With pagination, you:
    - Only fetch a small chunk of data
    - Load results page-by-page, just like Google search or online shops

#### 📷GetAllsBrand API
![GetAllsBrand API](./imgs/get_all_brand_api.png)

[📬 Click here to open the_get_all_brands_request](https://www.postman.com/graduation-space-584306/commerceapi/request/dnsmo9g/commerceapi?action=share&creator=21090382&ctx=documentation)

### 9. Get Brand by ID
**Endpoint:** `GET /api/v1/brand/getBrandById/brandId`  
**Description:** Fetches a single brand document from the database using the brand's unique ID. Returns the brand data if found, otherwise responds with an error indicating the brand was not found.
- **Authentication:**: Bearer__<jwt_token>
- **Authorization:** Admin, User
  
Successful Response (200 OK):
```json
{
    "message": "Done",
    "Brand": {
        "_id": "683da1adddadfbcacc03354f",
        "name": "brand name edited",
        "image": "https://res.cloudinary.com/ds7wrpkx4/image/upload/v1748869548/brands/hvtvrvopudguthm552p4.jpg",
        "public_id": "brands/hvtvrvopudguthm552p4",
        "slug": "brand-name-edited",
        "createdBy": "683c4fad80b62da136c98c9d",
        "createdAt": "2025-06-02T13:05:49.072Z",
        "updatedAt": "2025-06-02T13:07:00.009Z",
        "__v": 0
    }
}
```

#### 📷GetBrandByID API
![GetBrandByID API](./imgs/get_brand_by_id_req_res.png)

[📬 Click here to open the_brand_by_id_request](https://www.postman.com/graduation-space-584306/commerceapi/request/7yaadxo/commerceapi?action=share&creator=21090382&ctx=documentation)


### 10. Delete Brand By ID
**Endpoint:** `DELETE /api/v1/brand/deleteBrandById/brandId`  
**Description:** Deletes a brand from the database by its ID. This route includes an authorization check to ensure only the user who created the brand (the owner) can delete it. Returns the deleted brand data if successful.
- **Authentication:**: Bearer__<jwt_token>
- **Authorization:** Admin, User
  
Successful Response (200 OK):
```json
{
    "message": "Done",
    "deletedBrand": {
        "acknowledged": true,
        "deletedCount": 1
    }
}
```

### Before Deletion:
![Before_deleteion_API](./imgs/brands_before_delete.png)

### After Deletion:
![After_deleteion_API](./imgs/brands_after_delete.png)

#### 📷DeleteBrandByID API
![DeleteBrandByID_API](./imgs/delete_brand_req_res.png)

[📬 Click here to open the_delete_brand_by_ID_request](https://www.postman.com/graduation-space-584306/commerceapi/request/fny5w63/commerceapi?action=share&creator=21090382&ctx=documentation)

## 📌 Future Improvements
- Add payment integration

## 📊 Auth API Endpoints
| Method  | Route                | Description                         |
| ------  | ------------------   | --------------------------------    |
| POST    | /api/v1/auth/signUp  | Register a new user account         |
| POST    | /api/v1/auth/logIn   | Authenticate user and return token  |
| PUT     | /api/v1/updateRole   | Promote user to Admin after email confirmation |
| POST    | /api/v1/auth/sendCode      | Send OTP to user email in forget pass case |
| POST    | /api/v1/auth/forgetPassword      | Reset password using email and OTP |
| ...     | ...                  | ...                                 |


## 🔗 Postman Collection

You can test all API endpoints using the following Postman collection:

[📬 Click here to open the Postman collection](https://www.postman.com/graduation-space-584306/commerceapi/collection/v1gadmp/commerceapi?action=share&creator=21090382)

## 🔐 Authorization Note:
After logging in, ensure you set the Authorization header with the Bearer token in your API requests. This token is required to access protected routes and perform actions based on your authorized role.

Deployed on Vercel

## 👩‍💻 Author
Maryam Mohamed
Backend Developer

