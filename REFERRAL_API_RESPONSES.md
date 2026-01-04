# Referral Discount System - API Response Data

## 1. User Registration with Referral Code

### Endpoint
`POST /api/frontend/user/register`

### Request Body
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "phoneNumber": "+1234567890",
  "gender": "MALE",
  "device_token": "firebase_device_token_here",
  "userType": "RIDER",
  "referralCode": "ABC12345"
}
```

### Success Response (201)
```json
{
  "status": "success",
  "error": null,
  "message": "Registration successful. Please verify your phone number with the OTP sent.",
  "data": {
    "user": {
      "id": 123,
      "firstName": "John",
      "lastName": "Doe",
      "fullName": "John Doe",
      "email": "john.doe@example.com",
      "phoneNumber": "+1234567890",
      "status": false,
      "isVerified": false,
      "device_token": "firebase_device_token_here",
      "picture": null,
      "otpCode": "123456"
    }
  }
}
```

### Error Response - Invalid Referral Code (400)
```json
{
  "status": "error",
  "error": {
    "error": "Invalid referral code"
  },
  "message": "The referral code you entered is invalid or does not exist"
}
```

### Error Response - Self Referral (400)
```json
{
  "status": "error",
  "error": {
    "error": "Cannot use your own referral code"
  },
  "message": "Cannot use your own referral code"
}
```

### Error Response - Already Used Referral (400)
```json
{
  "status": "error",
  "error": {
    "error": "You have already used a referral code"
  },
  "message": "You have already used a referral code"
}
```

---

## 2. Validate Referral Code

### Endpoint
`GET /api/frontend/user/validate-referral-code/:code`

### Request
```
GET /api/frontend/user/validate-referral-code/ABC12345
```

### Success Response - Valid Code (200)
```json
{
  "status": "success",
  "error": null,
  "message": "Referral code is valid",
  "data": {
    "valid": true,
    "code": "ABC12345",
    "referrerName": "Jane Smith",
    "referrerId": 100
  }
}
```

### Success Response - Invalid Code (200)
```json
{
  "status": "success",
  "error": null,
  "message": "Referral code is invalid",
  "data": {
    "valid": false,
    "code": "INVALID123",
    "referrerName": null,
    "referrerId": null
  }
}
```

---

## 3. Get Referral Discount Balance

### Endpoint
`GET /api/frontend/user/referral-discount-balance`

### Request Headers
```
Authorization: Bearer <jwt_token>
```

### Success Response (200)
```json
{
  "status": "success",
  "error": null,
  "message": "Referral discount balance retrieved successfully",
  "data": {
    "balance": 5.00,
    "currency": "EUR",
    "available": true,
    "expiresAt": null
  }
}
```

### Success Response - No Balance (200)
```json
{
  "status": "success",
  "error": null,
  "message": "Referral discount balance retrieved successfully",
  "data": {
    "balance": 0.00,
    "currency": "EUR",
    "available": false,
    "expiresAt": null
  }
}
```

---

## 4. Get Referral History

### Endpoint
`GET /api/frontend/user/referral-history`

### Query Parameters (Optional)
```
?type=sent|received|all
```

### Success Response (200)
```json
{
  "status": "success",
  "error": null,
  "message": "Referral history retrieved successfully",
  "data": {
    "sentReferrals": [
      {
        "id": 456,
        "referrerId": 100,
        "refereeId": 123,
        "status": "COMPLETED",
        "rewardForReferrerAmount": 5.00,
        "rewardForRefereeAmount": 5.00,
        "completionCondition": "FIRST_RIDE_COMPLETED",
        "completedAt": "2024-01-20T14:30:00.000Z",
        "createdAt": "2024-01-15T10:30:00.000Z",
        "referee": {
          "id": 123,
          "firstName": "John",
          "lastName": "Doe",
          "fullName": "John Doe",
          "email": "john.doe@example.com"
        }
      },
      {
        "id": 457,
        "referrerId": 100,
        "refereeId": 124,
        "status": "PENDING",
        "rewardForReferrerAmount": 5.00,
        "rewardForRefereeAmount": 5.00,
        "completionCondition": "FIRST_RIDE_COMPLETED",
        "completedAt": null,
        "createdAt": "2024-01-18T09:15:00.000Z",
        "referee": {
          "id": 124,
          "firstName": "Bob",
          "lastName": "Johnson",
          "fullName": "Bob Johnson",
          "email": "bob.johnson@example.com"
        }
      }
    ],
    "receivedReferral": {
      "id": 458,
      "referrerId": 100,
      "refereeId": 123,
      "status": "COMPLETED",
      "rewardForReferrerAmount": 5.00,
      "rewardForRefereeAmount": 5.00,
      "completionCondition": "FIRST_RIDE_COMPLETED",
      "completedAt": "2024-01-20T14:30:00.000Z",
      "createdAt": "2024-01-15T10:30:00.000Z",
      "referrer": {
        "id": 100,
        "firstName": "Jane",
        "lastName": "Smith",
        "fullName": "Jane Smith",
        "email": "jane.smith@example.com"
      }
    },
    "statistics": {
      "totalSent": 2,
      "totalCompleted": 1,
      "totalPending": 1,
      "totalEarned": 5.00,
      "totalReceived": 5.00
    }
  }
}
```

---

## 5. Request Ride (Updated with Referral Discount)

### Endpoint
`POST /api/frontend/ride/request`

### Request Body
```json
{
  "pickupCity": "New York",
  "pickupState": "NY",
  "pickupCountry": "USA",
  "pickupAddress": "123 Main St",
  "pickupLatitude": 40.7128,
  "pickupLongitude": -74.0060,
  "dropoffCity": "New York",
  "dropoffState": "NY",
  "dropoffCountry": "USA",
  "dropoffAddress": "456 Broadway",
  "dropoffLatitude": 40.7580,
  "dropoffLongitude": -73.9855,
  "categoryId": 1,
  "serviceTypeId": 1
}
```

### Success Response (201)
```json
{
  "status": "success",
  "error": null,
  "message": "Ride requested successfully",
  "data": {
    "rideRequest": {
      "id": 789,
      "rideNumber": "1234567890",
      "userId": 123,
      "categoryId": 1,
      "status": "REQUESTED",
      "distance": 5.2,
      "estimatedFare": 25.50,
      "estimatedTime": 15,
      "discount": 5.00,
      "couponCode": null,
      "pickupLocation": {
        "id": 1,
        "address": "123 Main St",
        "city": "New York",
        "latitude": 40.7128,
        "longitude": -74.0060
      },
      "dropoffLocation": {
        "id": 2,
        "address": "456 Broadway",
        "city": "New York",
        "latitude": 40.7580,
        "longitude": -73.9855
      },
      "type": "BOOK_NOW",
      "scheduledAt": null
    },
    "noOfDriversNotified": 3,
    "drivers": [...]
  }
}
```

---

## 6. Get Category Wise Pricing (Updated with Referral Discount)

### Endpoint
`POST /api/frontend/ride/category-pricing`

### Request Body
```json
{
  "pickupLatitude": 40.7128,
  "pickupLongitude": -74.0060,
  "dropoffLatitude": 40.7580,
  "dropoffLongitude": -73.9855,
  "couponCode": null
}
```

### Success Response (200)
```json
{
  "status": "success",
  "error": null,
  "message": "Category list fetched successfully",
  "data": {
    "categories": [
      {
        "id": 1,
        "categoryName": "Economy",
        "slug": "economy",
        "status": true,
        "IconMediaId": 10,
        "categoryImage": "https://example.com/image.jpg",
        "price": 25,
        "discount": 0,
        "referralDiscount": 5,
        "totalDiscount": 5,
        "finalPrice": 20,
        "couponCode": null,
        "estimatedTime": 15,
        "distance": 5.2,
        "totalSeat": 4
      },
      {
        "id": 2,
        "categoryName": "Premium",
        "slug": "premium",
        "status": true,
        "IconMediaId": 11,
        "categoryImage": "https://example.com/image2.jpg",
        "price": 45,
        "discount": 0,
        "referralDiscount": 5,
        "totalDiscount": 5,
        "finalPrice": 40,
        "couponCode": null,
        "estimatedTime": 15,
        "distance": 5.2,
        "totalSeat": 4
      }
    ],
    "availableReferralDiscount": 5.00,
    "currency": "EUR"
  }
}
```

### Success Response - No Referral Discount (200)
```json
{
  "status": "success",
  "error": null,
  "message": "Category list fetched successfully",
  "data": {
    "categories": [
      {
        "id": 1,
        "categoryName": "Economy",
        "price": 25,
        "discount": 0,
        "referralDiscount": 0,
        "totalDiscount": 0,
        "finalPrice": 25
      }
    ],
    "availableReferralDiscount": 0.00,
    "currency": "EUR"
  }
}
```

---

## 7. Process Payment (Updated with Referral Discount)

### Endpoint
`POST /api/frontend/payment/process/:rideId`

### Request Body
```json
{
  "paymentMethod": "WALLET",
  "bonusAmount": 0
}
```

### Success Response (200)
```json
{
  "status": "success",
  "error": null,
  "message": "Payment processed successfully.",
  "data": {
    "paymentMethod": "WALLET",
    "amount": 20.50,
    "referralDiscount": 5.00,
    "rideId": 789,
    "status": "COMPLETED",
    "referralCompletion": {
      "completed": true,
      "referral": {
        "id": 456,
        "referrerId": 100,
        "refereeId": 123,
        "status": "COMPLETED",
        "completedAt": "2024-01-20T15:30:00.000Z"
      },
      "discountCredited": {
        "referrer": 5.00,
        "referee": 5.00
      }
    }
  }
}
```

### Success Response - No Referral Completion (200)
```json
{
  "status": "success",
  "error": null,
  "message": "Payment processed successfully.",
  "data": {
    "paymentMethod": "WALLET",
    "amount": 25.50,
    "referralDiscount": 0.00,
    "rideId": 789,
    "status": "COMPLETED",
    "referralCompletion": {
      "completed": false,
      "message": "No pending referral found"
    }
  }
}
```

---

## 8. Get User Profile (Updated with Referral Info)

### Endpoint
`GET /api/frontend/user/profile`

### Success Response (200)
```json
{
  "status": "success",
  "error": null,
  "message": "User profile retrieved successfully",
  "data": {
    "id": 123,
    "firstName": "John",
    "lastName": "Doe",
    "fullName": "John Doe",
    "email": "john.doe@example.com",
    "phoneNumber": "+1234567890",
    "userType": "RIDER",
    "referralCode": "XYZ98765",
    "usedReferralCode": "ABC12345",
    "referralDiscountBalance": 0.00,
    "isVerified": true,
    "status": true,
    "createdAt": "2024-01-15T10:30:00.000Z"
  }
}
```

---

## 9. Update Ride Status (Triggers Referral Completion)

### Endpoint
`PATCH /api/frontend/ride/update-status/:rideId`

### Request Body
```json
{
  "status": "COMPLETED",
  "latitude": 40.7580,
  "longitude": -73.9855,
  "actualDistance": 5.5,
  "actualTime": 18,
  "waitingTime": 2
}
```

### Success Response (200)
```json
{
  "status": "success",
  "error": null,
  "message": "Ride status updated successfully",
  "data": {
    "id": 789,
    "status": "COMPLETED",
    "userId": 123,
    "driverId": 50,
    "endTime": "2024-01-20T15:45:00.000Z",
    "actualDistance": 5.5,
    "actualTime": 18,
    "waitingTime": 2,
    "actualFare": 20.50
  }
}
```

---

## 10. Error Response Format (Standard)

### 400 Bad Request
```json
{
  "status": "error",
  "error": {
    "error": "Bad request details"
  },
  "message": "Bad request"
}
```

### 401 Unauthorized
```json
{
  "status": "error",
  "error": {
    "error": "Unauthorized"
  },
  "message": "Unauthorized"
}
```

### 403 Forbidden
```json
{
  "status": "error",
  "error": {
    "error": "Forbidden"
  },
  "message": "Forbidden"
}
```

### 404 Not Found
```json
{
  "status": "error",
  "error": {
    "error": "Resource not found"
  },
  "message": "Resource not found"
}
```

### 500 Internal Server Error
```json
{
  "status": "error",
  "error": {
    "error": "Internal server error"
  },
  "message": "Failed to process request"
}
```

---

## Response Field Descriptions

### Referral Status Values
- `PENDING` - Referral created but not completed
- `COMPLETED` - Referral completed (first ride done)
- `EXPIRED` - Referral expired (time limit exceeded)
- `CANCELLED` - Referral cancelled (fraud or manual)

### Currency
All monetary values are in **EUR** (based on codebase configuration).

### Timestamps
All timestamps are in **ISO 8601 format (UTC)**.

### Discount Limits
- Maximum referral discount per ride: **$5.00**
- Referral discount cannot exceed ride fare
- Referral discount is applied before tax calculation

---

## Notes

1. All endpoints follow the standard response format: `{ status, error, message, data }`
2. Referral discount is automatically applied when user has balance > 0
3. Discount balance is deducted when ride is requested
4. Referral completion happens automatically after first ride is completed
5. Both referrer and referee receive $5 discount when referral is completed
