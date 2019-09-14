# Session Invalidation on Privilege Changed

This is a hard problem since in essence using DB to store JWT token defeats the purpose of using JWT token in the first place. However, it is hard to almost impossible to invalidate JWT token access without saving some data in the database.

There are 2 approaches with JWT token: 
1. Without refresh token - long-lived bearer token
2. With refresh token - short-lived bearer token that can be refreshed on every call to the API

Solution: Record time of password change event, and any access tokens created before the event will be automatically invalidated.

This constant check to the database may cause some performance issues, but it is better than saving access/session/refresh tokens in the database
