
# Virtual learn API




## API Reference

#### Register user - Send OTP to given phone number

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/signup/otp
```
 INPUT FORMAT
| Body (raw)(JSON) | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `number` | `String` | 10 digit phone number beginning with contry code (eg. +91) **Required**. |

OUTPUT FORMAT (Output will be in the form of JSON object)
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message": "Otp sent"` | OTP is sent to the phone number provided |
|`409`|`"message": "User already registered"`| User can reset password using phone number or login using username|

#### Register user - Verify phone number

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/signup/verify
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number provided before **Required**.|
| `otp`      | `Number` | OTP received on the phone number **Required**.  |

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Phone Number Verified"` | User can now be registered using this number.|
|`401`| `"message" : "Incorrect OTP"` | OTP entered is wrong |

#### Register user - Create account using user details (Will receive authentication key after registration)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/signup/register
```
 INPUT FORMAT

| Body | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `number` | `String` | **Required**. |
| `fullname` | `String` | **Required**. |
| `username` | `String` | **Required**. |
| `email` | `String` | **Required**. |
| `password` | `String` | **Required**. |

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
|`200`| ` "message": "User registered successfully"` | Authentication key will be generated(to be used when required).|
||                                    `"token": <AUTH KEY>`| |
| `408`      | ` "message": "Session expired"` | Request timeout, go back to user registration(send otp).|


#### Login (Authentication key will be received)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/login
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number provided before **Required**.|
| `otp`      | `Number` | OTP received on the phone number **Required**.  |

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message": "Login Successful"` | Username and password entered exists and matches. Auth Key is generated.|
|| `"token": <AUTH KEY> ` | |
| `404`      | `"message": "User dosen't exist"` | Username entered does not exist. Mention login failed. |
| `401`      | ` "message": "Login failed"` | Password entered is wrong. Mention login failed. |


#### Forgot Password (on login page) (Otp will be sent to the given number if the user has registered using the same before)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/forgotpassword/otp
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number that was used to create the account **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Otp sent" ` | OTP is sent to the given number.|
| `404`      | `"message" : "User does not exist"` | Phone number was not used to register. Go to user registration.|


#### Verify OTP (for forgot password (on login page))

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/forgotpassword/verify
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number that was used to create the account **Required**.|
| `otp`      | `Number` | OTP received on the phone number **Required**.  |


OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Phone Number Verified"` | User can now reset password using this number.|
|`401`| `"message" : "Incorrect OTP"` | OTP entered is wrong |

#### Reset password (Possible if otp is correct)

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/user/forgotpassword/resetpassword
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number that was used to create the account **Required**.|
| `password`      | `String` | New password **Required**.  |

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "message" : "Password changed successfully"` | Go to login. Login with username and new password. |
| `408`      | `"message": "Session expired"` | User took too long to respond. Resend otp and verify.|


#### Get user details

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/user/displayprofile
```
 INPUT FORMAT

| Header (API KEY) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200 (OK)`      | `"number" : <User's phone number> ` | User details  are displayed.| 
|      | `"fullname" : <User's full name> ` | | 
|      | `"username" : <User's username> ` | | 
|      | `"email" : <User's mail id> ` | | 
|      | `"ccupation" : <User's Occupation> ` | | 
|      | `"dateOfBirth" : <User's Date Of Birth> ` | | 
|      | `"gender" : <User's Gender> ` | | 
|      | `"twitterLink" : <Link to user's twitter profile> ` | | 
|      | `"facebookLink" : <Link to user's facebook profile> ` | | 
|      | `"coursesJoined" : <An array of the course ID's that the user has enrolled in> ` | | 
|      | `"image" : <Link in database of user's profile picture> ` | | 
|      | `"_id" : <User's id (in the database)> ` | | 
|`401`| ` "message": "Authentication Failed" ` |Check auth key|


#### Update user details

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/user/updateprofile
```
 INPUT FORMAT

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

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Profile updated successfully"` | Profile updated. |
| `400`      | `"message": "Failed to update"` | Profile is not updated. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|

#### Upload user profile picture

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/user/updateprofile
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body (form-data) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `profileImage`      | `file` | Image in jpeg/png format  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Picture uploaded successfully"` | Photo uploaded. |
| `400`      | `"message": "Failed to upload"` | Failed to upload photo. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|

#### Change password providing current password

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/user/changepassword/oldpassword
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `oldpassword`      | `String` | User's current password  **Required**.|
| `newpassword`      | `String` | New password  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message": "Password changed successfully"` | Password changed. |
| `400`      | `"message": "Wrong Password, Try forgot password"` | Old password is incorrect. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|


#### Change password using phone number (send otp)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/changepassword/otp
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message": "Otp sent"` | OTP is sent to the user's phone number. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|

#### Change password using phone number (Verify otp)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/user/changepassword/verify
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `otp`      | `Number` | OTP received on the phone number  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "message" : "Phone Number Verified" ` | Now password of the user can be reset. |
| `400` | ` "message" : "Incorrect OTP" ` | Wrong OTP. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|

#### Change password using phone number (Reset password)

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/user/changepassword/newpassword
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `password`      | `String` | New password  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
|`200`| ` "message": "Password changed successfully" ` |  Password changed. |
| `408`      | `"message": "Session expired"` | Resend and verify OTP, current session is expired. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|

#### Get all courses

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/search/
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login |

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "courses" : <course schema>` | All courses available will be displayed. |
| `404`      | `"message": "No course available" ` | Course DB is empty. |


#### Get a course by name

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/search/getcoursebyname
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `name`      | `String` | Name of the course to be searched  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "courses" : <course schema>  ` | All courses with the given name will be displayed. |
| `404`      | `"message" : "Course not found" ` | Course with the given name is not found. |



#### Get all courses in a given category

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/search/getallincategory
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `category`      | `String` | Category required  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "courses" : <course schema>  ` | All courses under the given category will be displayed. |
| `404`      | `"message" : "Category not found"  ` | Category unavailable. |

#### Enroll in a course

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/search/enroll
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Admin auth key **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `_id`      | `String` | ID of the course to be enrolled  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "message" : "Successfully enrolled" ` | User is enrolled in course |
| `404`      | ` "message" : "Cant enroll, course not found" ` | Course ID incorrect. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|

#### Get all enrolled courses

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/user/getenrolledcourses
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"coursesEnrolled" : user[0].coursesJoined` | ID of the courses enrolled by the user. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key (Authorization key in header)|
