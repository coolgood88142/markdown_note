# How to design better APIs

## 大綱

- Be consistent
- Use ISO 8601 UTC dates
- Make an exception for public endpoints
- Provide a health check endpoint
- Version the API
- Accept API key authentication
- Use reasonable HTTP methods
- Use self-explanatory, simple names
- Use standardized error responses
- Return created resources upon POST
- Prefer PATCH over PUT
- Be as specific as possible
- Use pagination
- Allow expanding resources

## Be consistent(保持一致)

變數名稱、Route保持一致英文大小寫，一般看到都是駝峰式(getUserData)、底線(get_user_data)

這樣設計API或參數，會比較好維護

### Use ISO 8601 UTC dates(保持一致)

```
{
    "published_at": "2022-03-03T21:59:08Z"
}
```

### Make an exception for public endpoints(對外的端點)

每個端點都會需要權限，都要授權透過身分驗證，必須要提供重要資料，而公共的端點，要設定成未允許授權，通常都是提供可對外公開的資料

### Provide a health check endpoint(提供可通過的端點)

GET提供正常的檢查資料API，預防查詢資料過多，會有資料負荷的情況

### Version the API

為了確保能很清楚資料API版本，可以在route加version

```
https://api.averagecompany.com/v1/health
https://api.averagecompany.com/health?api_version=1.0
```

### 



### 參考資料

- [How to design better APIs]([How to design better APIs](https://r.bluethl.net/how-to-design-better-apis?utm_source=newsletter&utm_medium=email&utm_campaign=starrocket&utm_term=2022-05-23))
