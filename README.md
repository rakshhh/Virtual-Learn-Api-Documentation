
# Virtual learn API




## API Reference

#### Register user - Send OTP to given phone number

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/signup/otp
```

| Body (raw)(JSON) | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `number` | `String` | 10 digit phone number beginning with contry code (eg. +91) **Required**. |

#### Regiter user - Verify phone number

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/signup/verify
```

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number provided before **Required**.|
| `otp`      | `Number` | OTP received on the phone number **Required**.  |

#### Register user - Create account using user details (Will receive authentication key after registration)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/signup/register
```

| Body | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `number` | `String` | **Required**. |
| `fullname` | `String` | **Required**. |
| `username` | `String` | **Required**. |
| `email` | `String` | **Required**. |
| `password` | `String` | **Required**. |


#### Login (Authentication key will be received)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/login
```

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number provided before **Required**.|
| `otp`      | `Number` | OTP received on the phone number **Required**.  |

#### Forgot Password (on login page) (Otp will be sent to the given number if the user has registered using the same before)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/forgotpassword/otp
```

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number that was used to create the account **Required**.|


#### Verify OTP (for forgot password (on login page))

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/forgotpassword/verify
```

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number that was used to create the account **Required**.|
| `otp`      | `Number` | OTP received on the phone number **Required**.  |


#### Reset password (Possible if otp is correct)

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/user/forgotpassword/resetpassword
```

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number that was used to create the account **Required**.|
| `password`      | `String` | New password **Required**.  |


#### Get user details

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/user/displayprofile
```

| Header (API KEY) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

#### Update user details

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/user/updateprofile
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `fullname`      | `String` | Full name |
| `occupation`      | `String` | Occupation |
| `dateOfBirth`      | `String` | Date of birth |
| `gender`      | `String` | Gender |
| `twitterLink`      | `String` | Link to user's Twitter profile |
| `facebookLink`      | `String` | Link to user's Facebook profile |
| `email`      | `String` | User's email ID |

#### Upload user profile picture

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/user/updateprofile
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body (form-data) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `profileImage`      | `file` | Image in jpeg/png format  **Required**.|

#### Change password providing current password

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/user/changepassword/oldpassword
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `oldpassword`      | `String` | User's current password  **Required**.|
| `newpassword`      | `String` | New password  **Required**.|


#### Change password using phone number (send otp)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/changepassword/otp
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

#### Change password using phone number (Verify otp)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/changepassword/verify
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `otp`      | `Number` | OTP received on the phone number  **Required**.|

#### Change password using phone number (Reset password)

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/user/changepassword/newpassword
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `password`      | `String` | New password  **Required**.|

#### Get all courses

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/search/
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login |

#### Get a course by name

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/search/getcoursebyname
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `name`      | `String` | Name of the course to be searched  **Required**.|

#### Get all courses in a given category

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/search/getallincategory
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `category`      | `String` | Category required  **Required**.|

#### Enroll in a course

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/search/enroll
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Admin auth key **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `_id`      | `String` | ID of the course to be enrolled  **Required**.|

#### Get all enrolled courses

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/user/getenrolledcourses
```

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|


