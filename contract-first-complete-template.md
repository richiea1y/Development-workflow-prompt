# Contract-First API 架構完整開發規範模板

此模板專為 Claude Code 設計，包含完整的工作流程、回覆格式和所有開發規範。

## 🎯 Claude Code 工作指引

### 回覆格式要求

**簡潔性原則**：
- 回應必須簡潔，少於 4 行（不包括工具使用或程式碼生成）
- 最小化輸出 tokens，避免不必要的前言和後語
- 一個字回答是最好的，避免介紹、結論和解釋
- 禁止使用「答案是...」、「這是檔案內容...」、「基於提供的資訊...」等無用前綴

**CLI 友好格式**：
- 使用 GitHub 風格的 markdown
- 避免 markdown 表格（CLI 不友好）
- 簡潔的文字區塊
- 重點資訊用符號標註（✅ ❌ 📁 等）
- 用清晰標題分組（📁 檔案: 檔名）

**程式碼審閱格式**：
每個修改點標註「修改點 X: 描述 (行號)」，使用 Git diff 格式顯示變更：
```
- 刪除的內容
+ 新增的內容
```

每個修改點下方三行說明：
- 改進點: [具體改進內容]
- 效益: [帶來什麼好處]（效益與原因之間要有空格）
- 原因: [為什麼這樣改，具體說明新方法的實際應用，詳細分析帶來好處的原因]

### 工作模式要求

**計劃-執行模式**（強制）：
1. **計劃階段**:
   - 分析需求和現有程式碼
   - 使用 TodoWrite 工具建立詳細任務分解
   - 向使用者展示計劃並等待確認
   - 等待核准後才開始進行

2. **執行階段**:
   - 按照計劃逐步執行任務
   - 即時更新 todo 狀態（只能有一個 in_progress）
   - 立即標記完成的任務為 completed
   - 報告每個步驟的完成情況

**指示事項**：
- 收到任務時絕不立即開始編程
- 必須先進入「計劃模式」
- 只有在使用者確認計劃後才進入「執行模式」

## 🔄 Vue 前端開發前置作業

### DRY 原則分析與重構前置檢查

程式碼修改前的必要分析流程：

**1. DRY (Don't Repeat Yourself) 原則檢查**
- 掃描待修改區域是否存在重複 patterns
- 識別可抽象化的共同邏輯和結構
- 評估重複次數（≥2 次必須考慮重構）
- 分析重複程式碼的變異點和不變點

**2. 重構效益評估**
- 計算可節省的程式碼行數
- 評估維護性改善程度
- 分析一致性提升效果
- 估算長期開發效率收益

**3. 重構風險評估**
- 確認修改範圍和影響層面
- 識別可能的破壞性變更
- 制定回滾策略和測試計劃
- 遵循最小可行重構原則

**4. 抽象化設計分析**
- 設計可重用的 interface/type 定義
- 規劃 composable/component 的 API 設計
- 考慮未來擴展性和靈活性
- 確保符合專案架構規範

### 開發前置作業流程

**🔄 開發前置檢查**：
- 在 update code 之前都要先說明重構設計想法，並且清楚解釋為什麼要這麼做、怎麼做
- 分析現有程式碼結構，識別重複模式和可優化點
- 確認修改範圍和影響層面

## 📋 規範條文

### 最小可行重構原則 (MVP Refactoring Principle)

- 每次重構必須是最小可行單位，確保程式碼完整性和可運行性
- 每個 commit 都應該是獨立可部署的狀態，不破壞現有功能
- 重構順序應遵循「先建立新資源，再逐步替換舊實作」的漸進式策略
- 避免大範圍同時修改多個檔案，降低回滾風險

### 術語統一規範

- 專業技術名詞使用英文，保持國際化和精確性
- 元件 → component/components
- 佈局 → layout/layouts  
- 服務 → service/services
- 介面/接口 → interface/interfaces
- 型別 → type/types
- 屬性 → property/properties 或 prop/props
- 函式/方法 → function/functions 或 method/methods
- 模組 → module/modules
- 路由 → route/routes
- 狀態 → state
- 重構 → refactor/refactoring

## 🔍 開發新功能時的完整決策流程

### 第一階段：查找現有資源

開發新功能前必須先檢查：
```
├─ 需要資料結構？
│ └─ 檢查 src/services/schema/ 是否有可重用的 schemas
├─ 需要 API 規格？
│ └─ 檢查 src/services/contract/ 是否有可重用的 contracts
├─ 需要 API 實作？
│ └─ 檢查 src/services/api/ 是否有可重用的 APIs
├─ 需要佈局結構？
│ └─ 檢查 src/layouts/ 是否有可重用的佈局元件
├─ 需要 UI 元件？
│ ├─ 檢查 src/components/ 是否有可重用的全域元件
│ └─ 檢查相關 pages/_/components/ 是否有類似元件
└─ 需要業務邏輯？
├─ 檢查 src/composables/ 是否有可重用的全域邏輯
└─ 檢查相關 pages/_/composables/ 是否有類似邏輯
```

### 第二階段：創建或重構決策

找不到可重用的資源時：
```
├─ 是資料結構相關？（Zod schemas、型別定義）
│ └─ 先創建到對應模組的 schema 檔案
├─ 是 API 規格相關？（契約定義、端點規格）
│ └─ 先創建到對應模組的 contract 檔案
├─ 是 API 實作相關？（HTTP 請求、資料處理）
│ └─ 先創建到對應模組的 API 檔案
├─ 是佈局結構相關？（頁面架構、導航、版面配置）
│ └─ 創建到 src/layouts/ 根目錄
├─ 是 UI 元件？（按鈕、表單、卡片、顯示組件等）
│ └─ 先創建到 pages/_/components/（專屬位置）
└─ 是業務邏輯？
└─ 先創建到 pages/_/composables/（專屬位置）
```

### 第三階段：重構檢查（發現重複時）

當發現多個地方有重複元件/邏輯時：
```
├─ Schema 重複使用（≥2 個模組）？
│ └─ 重構移動到 src/services/schema/responses.ts（共用回應）
├─ Contract 重複使用（≥2 個模組）？
│ └─ 重構抽離共用契約介面到 src/services/contract/
├─ API 邏輯重複使用（≥2 個模組）？
│ └─ 重構抽離共用 API 邏輯到 src/services/api/
├─ 佈局結構重複使用（≥2 個模組）？
│ └─ 重構移動到 src/layouts/（全域位置）
├─ UI 元件重複使用（≥2 個模組）？
│ └─ 重構移動到 src/components/（全域位置）
└─ 業務邏輯重複使用（≥2 個模組）？
└─ 重構移動到 src/composables/（全域位置）
```

## 📂 強制目錄結構與職責分工

### Contract-First 架構層次

```
src/services/
├── schema/                  # Zod 資料驗證層（第一層）
│   ├── [模組名稱].ts        # 各功能模組 schemas
│   ├── responses.ts         # 共用回應 schemas（必須）
│   └── index.ts            # 統一匯出（必須）
├── contract/               # API 契約定義層（第二層）
│   ├── [模組名稱].ts        # 各功能模組契約
│   └── index.ts            # 統一匯出（必須）
├── api/                    # API 實作層（第三層）
│   ├── [模組名稱]Api.ts     # 基於契約的實作
│   └── index.ts            # 統一匯出（必須）
├── types/                  # 從 schemas 推斷的型別
│   └── index.ts            # 統一匯出
└── fetchService.ts         # HTTP 請求基礎設施
```

### 其他架構層次

**🏗️ Layouts (src/layouts/) - 佈局結構**
- 頁面整體架構和版面配置
- 導航結構和選單系統
- 影響整個應用程式的佈局框架

**🎨 Components - UI 元件**
- src/components/ - 全域共用的 UI 元件
- pages/\*/components/ - 特定模組專用的 UI 元件
- 純展示邏輯，接收 props 發出 events

**🧩 Composables - 業務邏輯**
- src/composables/ - 全域共用的業務邏輯
- pages/\*/composables/ - 特定模組專用的業務邏輯
- 狀態管理、API 呼叫、複雜計算

## 📝 型別定義規範

### Interface vs Type 使用時機

**使用 Interface 的情況**：
```typescript
// 1. 物件結構定義（可擴展）
interface UserConfig {
  id: string
  name: string
  settings: UserSettings
}

// 2. 可能需要擴展的契約定義
interface APIContract {
  method: 'GET' | 'POST' | 'PUT' | 'DELETE'
  path: string
  body?: z.ZodSchema
}

// 3. 類別介面定義
interface UserService {
  getUser(id: string): Promise<User>
  createUser(data: CreateUserRequest): Promise<CreatedResponse>
}
```

**使用 Type 的情況**：
```typescript
// 1. 聯合類型
type Status = 'pending' | 'completed' | 'failed'
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE'

// 2. 條件類型和泛型工具
type ApiResponse<T> = {
  data: T | null
  error: string | null
}

// 3. 從 Zod schema 推斷的類型
type UserEntity = z.infer<typeof userSchema.entity>

// 4. 函式類型定義
type ApiHandler<TRequest, TResponse> = (
  request: TRequest
) => Promise<ApiResponse<TResponse>>
```

### 泛型設計原則

**命名規範**：
```typescript
// 單一泛型：T, U, V
type Result<T> = T | null

// 多個泛型：使用描述性名稱
type ApiContract<TRequest, TResponse, TParams = never> = {
  method: HttpMethod
  path: string
  body?: TRequest
  pathParams?: TParams
  responses: Record<number, TResponse>
}

// 約束泛型
type EntitySchema<T extends z.ZodObject<any>> = {
  entity: T
  response: T
  createRequest: z.ZodObject<Omit<T['shape'], 'id' | 'createdTime' | 'updatedTime'>>
}
```

**泛型最佳實踐**：
```typescript
// ✅ 好的泛型設計
interface Repository<TEntity, TCreateRequest, TUpdateRequest> {
  getById(id: string): Promise<TEntity | null>
  create(data: TCreateRequest): Promise<TEntity>
  update(id: string, data: TUpdateRequest): Promise<TEntity>
}

// ❌ 避免過度泛型化
interface OverComplexRepository<T, C, U, Q, S, F> {
  // 太多泛型參數，難以理解和使用
}
```

### 型別組合與推斷

**型別組合模式**：
```typescript
// 基礎型別
type BaseEntity = {
  id: string
  createdTime: string
  updatedTime: string
}

// 組合型別
type User = BaseEntity & {
  name: string
  email: string
  isActive: boolean
}

// 條件型別
type CreateRequest<T> = Omit<T, keyof BaseEntity>
type UpdateRequest<T> = Partial<CreateRequest<T>>

// 使用範例
type UserCreateRequest = CreateRequest<User>
type UserUpdateRequest = UpdateRequest<User>
```

## 🏷️ 介面命名規範

### 命名慣例

**基本規則**：
- 不使用 `I` 前綴（避免 `IUser`，使用 `User`）
- 使用 PascalCase（`UserResponse`，不是 `userResponse`）
- 使用描述性名稱（`AdminUserCreateRequest`，不是 `CreateReq`）

**命名模式**：
```typescript
// ✅ 實體相關
interface User { ... }                    // 基礎實體
interface UserEntity { ... }              // 完整實體（包含關聯）
interface UserResponse { ... }            // API 回應格式
interface UserCreateRequest { ... }       // 新增請求
interface UserUpdateRequest { ... }       // 更新請求
interface UserQueryParams { ... }         # 查詢參數

// ✅ API 相關
interface APIContract { ... }             // API 契約基礎介面
interface UserAPIContract { ... }         // 使用者 API 契約
interface ResponseStructure<T> { ... }    // 回應結構泛型

// ✅ 配置相關
interface AppConfig { ... }               // 應用程式配置
interface DatabaseConfig { ... }          // 資料庫配置
interface AuthConfig { ... }              // 驗證配置

// ✅ 服務相關
interface UserService { ... }             // 使用者服務介面
interface EmailService { ... }            // 郵件服務介面
interface LoggerService { ... }           // 日誌服務介面
```

**避免的命名**：
```typescript
// ❌ 避免使用的命名
interface IUser { ... }                   // 不要使用 I 前綴
interface userResponse { ... }            // 不要使用 camelCase
interface Usr { ... }                     // 不要使用縮寫
interface UserResponseInterface { ... }   // 不要加 Interface 後綴
interface UserData { ... }                // Data 太模糊
interface UserInfo { ... }                // Info 太模糊
```

### 特殊情況命名

**事件處理介面**：
```typescript
interface UserCreatedEvent {
  type: 'USER_CREATED'
  payload: User
  timestamp: string
}

interface FormSubmitHandler {
  (data: FormData): void | Promise<void>
}
```

**回調函式介面**：
```typescript
interface ApiCallbacks<T> {
  onSuccess: (data: T) => void
  onError: (error: Error) => void
  onLoading: (isLoading: boolean) => void
}
```

**配置選項介面**：
```typescript
interface UserListOptions {
  pagination?: PaginationOptions
  filters?: UserFilters
  sorting?: SortingOptions
}
```

## 📚 JSDoc 註解規範

### 基本註解格式

**函式註解模板**：
```typescript
/**
 * 函式功能的簡短描述（一句話）
 * 
 * 可選的詳細描述，解釋函式的行為、用途和注意事項。
 * 可以跨多行，提供更詳細的說明。
 * 
 * @param paramName - 參數說明，描述參數的用途和類型
 * @param optionalParam - 可選參數說明
 * @returns 回傳值說明，描述回傳的內容和格式
 * @throws {ErrorType} 可能拋出的錯誤類型和情況
 * @example
 * // 使用範例
 * const result = await functionName(param1, param2)
 * console.log(result)
 * 
 * @see {@link RelatedFunction} 相關函式或文件
 * @since 1.0.0
 * @author 作者名稱
 */
async function exampleFunction(paramName: string, optionalParam?: number): Promise<ResultType> {
  // 實作內容
}
```

**詳細註解範例**：
```typescript
/**
 * 取得使用者列表，支援分頁和篩選功能
 * 
 * 此函式會向後端 API 發送請求取得使用者列表，支援多種查詢條件
 * 包括分頁、搜尋關鍵字、狀態篩選等。回傳的資料已經過驗證和格式化。
 * 
 * @param params - 查詢參數物件
 * @param params.page - 頁碼，從 1 開始，預設為 1
 * @param params.size - 每頁筆數，範圍 1-100，預設為 10
 * @param params.search - 搜尋關鍵字，會搜尋使用者名稱和信箱
 * @param params.isActive - 篩選啟用狀態，true 為啟用，false 為停用
 * @returns Promise 包含使用者列表和分頁資訊
 * @throws {ValidationError} 當查詢參數不符合格式要求時
 * @throws {NetworkError} 當網路請求失敗時
 * @throws {AuthenticationError} 當使用者未登入或權限不足時
 * 
 * @example
 * // 基本使用
 * const users = await getUserList({ page: 1, size: 10 })
 * 
 * @example  
 * // 使用搜尋和篩選
 * const activeUsers = await getUserList({
 *   page: 1,
 *   size: 20,
 *   search: 'john',
 *   isActive: true
 * })
 * 
 * @see {@link createUser} 新增使用者函式
 * @see {@link updateUser} 更新使用者函式
 * @since 1.0.0
 */
export const getUserList = async (params?: UserQueryParams): Promise<PaginatedResponse<User>> => {
  // 實作內容
}
```

### Interface 和 Type 註解

**Interface 註解模板**：
```typescript
/**
 * 使用者實體介面定義
 * 
 * 定義使用者的完整資料結構，包含所有必要欄位和可選欄位。
 * 此介面用於 API 回應、資料庫實體和前端狀態管理。
 * 
 * @interface User
 * @since 1.0.0
 * @example
 * const user: User = {
 *   id: "user-123",
 *   name: "張小明",
 *   email: "ming@example.com",
 *   isActive: true,
 *   createdTime: "2024-01-01T00:00:00Z",
 *   updatedTime: "2024-01-01T00:00:00Z"
 * }
 */
interface User {
  /** 使用者唯一識別碼，由系統自動生成 */
  id: string
  
  /** 使用者顯示名稱，長度 1-50 字元 */
  name: string
  
  /** 使用者電子郵件地址，必須符合 email 格式 */
  email: string
  
  /** 使用者帳號啟用狀態，true 為啟用，false 為停用 */
  isActive: boolean
  
  /** 
   * 帳號建立時間，ISO 8601 格式
   * @example "2024-01-01T00:00:00Z"
   */
  createdTime: string
  
  /** 
   * 最後更新時間，ISO 8601 格式
   * @example "2024-01-01T00:00:00Z"
   */
  updatedTime: string
}
```

**複雜型別註解**：
```typescript
/**
 * API 回應結構泛型
 * 
 * 統一所有 API 的回應格式，確保前端能夠一致地處理 API 回應。
 * 成功時 data 有值 error 為 null，失敗時 data 為 null error 有值。
 * 
 * @template T - 回應資料的類型
 * @since 1.0.0
 * @example
 * // 成功回應
 * const successResponse: ApiResponse<User> = {
 *   data: { id: "1", name: "張小明", ... },
 *   error: null
 * }
 * 
 * @example
 * // 錯誤回應
 * const errorResponse: ApiResponse<User> = {
 *   data: null,
 *   error: { code: "USER_NOT_FOUND", message: "使用者不存在" }
 * }
 */
type ApiResponse<T> = {
  /** 成功時的回應資料，失敗時為 null */
  data: T | null
  
  /** 錯誤時的錯誤資訊，成功時為 null */
  error: ApiError | null
}
```

### Composable 函式註解

**Composable 註解模板**：
```typescript
/**
 * 使用者管理相關的 Composable
 * 
 * 提供使用者的 CRUD 操作、狀態管理和表單驗證功能。
 * 此 composable 封裝了所有使用者相關的業務邏輯，可在多個元件中重用。
 * 
 * @param initialFilters - 初始查詢篩選條件
 * @returns 使用者管理的狀態和方法
 * 
 * @example
 * // 在 Vue 元件中使用
 * const {
 *   users,
 *   loading,
 *   error,
 *   fetchUsers,
 *   createUser,
 *   updateUser,
 *   deleteUser
 * } = useUserManagement({ isActive: true })
 * 
 * // 載入使用者列表
 * await fetchUsers({ page: 1, size: 10 })
 * 
 * // 新增使用者
 * const newUser = await createUser({
 *   name: "新使用者",
 *   email: "new@example.com"
 * })
 * 
 * @since 1.0.0
 */
export const useUserManagement = (initialFilters?: Partial<UserQueryParams>) => {
  // 實作內容
}
```

### 元件 Props 註解

**Vue 元件 Props 註解**：
```typescript
/**
 * 使用者資訊卡片元件的 Props 定義
 * 
 * @interface UserCardProps
 */
interface UserCardProps {
  /** 
   * 使用者資料物件
   * @required
   */
  user: User
  
  /** 
   * 是否顯示編輯按鈕
   * @default false
   */
  showEditButton?: boolean
  
  /** 
   * 卡片尺寸
   * @default 'medium'
   */
  size?: 'small' | 'medium' | 'large'
  
  /** 
   * 自訂 CSS 類別名稱
   * @example "my-custom-card border-rounded"
   */
  className?: string
}
```

### 常用 JSDoc 標籤

**完整標籤列表**：
```typescript
/**
 * @param {Type} name - 參數說明
 * @returns {Type} 回傳值說明
 * @throws {ErrorType} 可能的錯誤
 * @example 使用範例
 * @see 相關連結或函式
 * @since 版本號
 * @author 作者
 * @deprecated 標記為過時
 * @todo 待辦事項
 * @default 預設值
 * @required 必填欄位
 * @optional 可選欄位
 * @readonly 唯讀欄位
 * @async 非同步函式
 * @static 靜態方法
 * @override 覆寫父類方法
 * @implements 實作的介面
 * @extends 繼承的類別
 * @namespace 命名空間
 * @module 模組說明
 * @template 泛型參數
 * @typedef 類型定義
 * @callback 回調函式
 * @event 事件定義
 * @fires 觸發的事件
 * @listens 監聽的事件
 */
```

## 🎨 Components 架構規範

### 元件設計原則

**單一職責原則**：
```typescript
// ✅ 好的設計：單一職責
// UserAvatar.vue - 只負責顯示使用者頭像
interface UserAvatarProps {
  user: User
  size?: 'small' | 'medium' | 'large'
  showStatus?: boolean
}

// ❌ 不好的設計：職責混雜
// UserComponent.vue - 混合了太多職責
interface UserComponentProps {
  user: User
  showEditForm: boolean
  permissions: Permission[]
  onSave: (user: User) => void
  onDelete: (id: string) => void
  showAvatar: boolean
  // ... 太多不相關的屬性
}
```

**組合優於繼承**：
```vue
<!-- ✅ 好的設計：使用組合 -->
<template>
  <UserCard>
    <UserAvatar :user="user" size="large" />
    <UserInfo :user="user" />
    <UserActions 
      :user="user" 
      @edit="handleEdit"
      @delete="handleDelete" 
    />
  </UserCard>
</template>

<!-- ❌ 不好的設計：單一巨大元件 -->
<template>
  <div class="user-everything">
    <!-- 混雜所有功能在一個元件中 -->
  </div>
</template>
```

### Props 設計規範

**Props 介面定義**：
```typescript
/**
 * 使用者卡片元件 Props
 * 遵循明確、可擴展、型別安全的設計原則
 */
interface UserCardProps {
  /** 使用者資料 - 必填 */
  user: User
  
  /** 卡片變體樣式 */
  variant?: 'default' | 'compact' | 'detailed'
  
  /** 是否顯示操作按鈕 */
  showActions?: boolean
  
  /** 自訂操作按鈕 */
  actions?: Array<{
    label: string
    icon?: string
    handler: () => void
    disabled?: boolean
  }>
  
  /** 載入狀態 */
  loading?: boolean
  
  /** 點擊卡片時的處理函式 */
  onClick?: (user: User) => void
}

// 元件定義
const props = defineProps<UserCardProps>()

// 預設值（使用 withDefaults）
const propsWithDefaults = withDefaults(defineProps<UserCardProps>(), {
  variant: 'default',
  showActions: true,
  loading: false
})
```

**Props 驗證和預設值**：
```typescript
// 複雜 Props 驗證
interface FormFieldProps<T = unknown> {
  /** 欄位名稱 */
  name: string
  
  /** 欄位值 */
  modelValue: T
  
  /** 欄位標籤 */
  label?: string
  
  /** 錯誤訊息 */
  error?: string
  
  /** 是否必填 */
  required?: boolean
  
  /** 是否停用 */
  disabled?: boolean
  
  /** 驗證規則 */
  rules?: Array<(value: T) => string | true>
  
  /** 欄位類型特定屬性 */
  fieldProps?: Record<string, unknown>
}

// 使用範例
<FormField
  name="email"
  v-model="formData.email"
  label="電子郵件"
  :required="true"
  :rules="[
    (v) => !!v || '請輸入電子郵件',
    (v) => /.+@.+\..+/.test(v) || '請輸入有效的電子郵件格式'
  ]"
  :field-props="{
    type: 'email',
    placeholder: '請輸入您的電子郵件地址'
  }"
/>
```

### Events 設計規範

**事件命名和類型定義**：
```typescript
/**
 * 使用者管理元件的事件定義
 */
interface UserManagementEvents {
  /** 使用者被選擇時觸發 */
  'user-selected': [user: User]
  
  /** 使用者資料更新時觸發 */
  'user-updated': [user: User, changes: Partial<User>]
  
  /** 使用者被刪除時觸發 */
  'user-deleted': [userId: string]
  
  /** 載入狀態改變時觸發 */
  'loading-changed': [isLoading: boolean]
  
  /** 錯誤發生時觸發 */
  'error-occurred': [error: Error]
}

// 元件中定義 emits
const emit = defineEmits<UserManagementEvents>()

// 觸發事件
const handleUserClick = (user: User) => {
  emit('user-selected', user)
}

const handleUserUpdate = (updatedUser: User, originalUser: User) => {
  const changes = getChanges(originalUser, updatedUser)
  emit('user-updated', updatedUser, changes)
}
```

**事件處理最佳實踐**：
```vue
<template>
  <!-- ✅ 好的事件處理 -->
  <UserCard
    :user="user"
    @user-selected="handleUserSelection"
    @user-updated="handleUserUpdate"
    @error-occurred="handleError"
  />
</template>

<script setup lang="ts">
// 清楚的事件處理函式
const handleUserSelection = (user: User) => {
  // 處理使用者選擇邏輯
  selectedUser.value = user
  // 可能還需要其他操作
}

const handleUserUpdate = (user: User, changes: Partial<User>) => {
  // 處理使用者更新邏輯
  updateUserInList(user)
  showSuccessMessage(`使用者 ${user.name} 已更新`)
}

const handleError = (error: Error) => {
  // 統一錯誤處理
  console.error('使用者操作錯誤:', error)
  showErrorMessage(error.message)
}
</script>
```

### 元件狀態管理

**內部狀態管理**：
```typescript
// 簡單元件狀態
const isOpen = ref(false)
const loading = ref(false)
const error = ref<string | null>(null)

// 複雜狀態使用 reactive
const state = reactive({
  users: [] as User[],
  selectedUsers: new Set<string>(),
  filters: {
    search: '',
    status: null as 'active' | 'inactive' | null,
    page: 1,
    size: 10
  },
  pagination: {
    total: 0,
    totalPages: 0
  }
})

// 計算屬性
const filteredUsers = computed(() => {
  return state.users.filter(user => {
    if (state.filters.search && !user.name.includes(state.filters.search)) {
      return false
    }
    if (state.filters.status && user.isActive !== (state.filters.status === 'active')) {
      return false
    }
    return true
  })
})

// 狀態操作方法
const selectUser = (userId: string) => {
  if (state.selectedUsers.has(userId)) {
    state.selectedUsers.delete(userId)
  } else {
    state.selectedUsers.add(userId)
  }
}

const clearSelection = () => {
  state.selectedUsers.clear()
}
```

### 元件樣式規範

**樣式組織和命名**：
```vue
<template>
  <div class="user-card" :class="cardClasses">
    <div class="user-card__header">
      <div class="user-card__avatar">
        <img :src="user.avatar" :alt="user.name" />
      </div>
      <div class="user-card__info">
        <h3 class="user-card__name">{{ user.name }}</h3>
        <p class="user-card__email">{{ user.email }}</p>
      </div>
    </div>
    
    <div class="user-card__content">
      <slot />
    </div>
    
    <div class="user-card__actions" v-if="showActions">
      <slot name="actions">
        <q-btn 
          flat 
          icon="edit" 
          @click="$emit('edit', user)"
          class="user-card__action-btn"
        />
      </slot>
    </div>
  </div>
</template>

<script setup lang="ts">
// 動態 CSS 類別
const cardClasses = computed(() => ({
  'user-card--compact': props.variant === 'compact',
  'user-card--detailed': props.variant === 'detailed',
  'user-card--loading': props.loading,
  'user-card--disabled': props.disabled
}))
</script>

<style lang="scss" scoped>
// BEM 命名規範
.user-card {
  @apply border rounded-lg p-4 bg-white shadow-sm;
  
  // 修飾符
  &--compact {
    @apply p-2;
    
    .user-card__header {
      @apply flex-row items-center space-x-2;
    }
  }
  
  &--detailed {
    @apply p-6;
  }
  
  &--loading {
    @apply opacity-50 pointer-events-none;
  }
  
  &--disabled {
    @apply opacity-60 cursor-not-allowed;
  }
  
  // 元素
  &__header {
    @apply flex flex-col space-y-2 mb-4;
  }
  
  &__avatar {
    @apply w-12 h-12 rounded-full overflow-hidden;
    
    img {
      @apply w-full h-full object-cover;
    }
  }
  
  &__info {
    @apply flex-1;
  }
  
  &__name {
    @apply text-lg font-semibold text-gray-900;
  }
  
  &__email {
    @apply text-sm text-gray-600;
  }
  
  &__content {
    @apply mb-4;
  }
  
  &__actions {
    @apply flex justify-end space-x-2;
  }
  
  &__action-btn {
    @apply text-primary;
    
    &:hover {
      @apply bg-primary/10;
    }
  }
}
</style>
```

## 🧩 Composables 架構規範

### Composable 設計原則

**單一職責和功能聚焦**：
```typescript
// ✅ 好的設計：專注於使用者 API 操作
export const useUserApi = () => {
  const getUsers = async (params?: UserQueryParams) => { ... }
  const createUser = async (data: UserCreateRequest) => { ... }
  const updateUser = async (id: string, data: UserUpdateRequest) => { ... }
  const deleteUser = async (id: string) => { ... }
  
  return {
    getUsers,
    createUser,
    updateUser,
    deleteUser
  }
}

// ✅ 好的設計：專注於使用者狀態管理
export const useUserState = () => {
  const users = ref<User[]>([])
  const selectedUser = ref<User | null>(null)
  const loading = ref(false)
  
  const setUsers = (newUsers: User[]) => { ... }
  const addUser = (user: User) => { ... }
  const updateUserInList = (updatedUser: User) => { ... }
  const removeUser = (userId: string) => { ... }
  
  return {
    users: readonly(users),
    selectedUser,
    loading: readonly(loading),
    setUsers,
    addUser,
    updateUserInList,
    removeUser
  }
}

// ❌ 不好的設計：功能過於龐雜
export const useEverything = () => {
  // 混合了 API、狀態、表單、權限、通知等太多功能
}
```

### 狀態共享模式

**全域狀態 vs 區域狀態**：
```typescript
// 全域狀態（函式外部宣告）- 跨元件共享
const globalUsers = ref<User[]>([])
const globalLoading = ref(false)

export const useGlobalUserState = () => {
  // 所有使用此 composable 的元件都會共享相同的狀態
  return {
    users: globalUsers,
    loading: globalLoading
  }
}

// 區域狀態（函式內部宣告）- 每個元件獨立實例
export const useLocalUserState = () => {
  const users = ref<User[]>([])
  const loading = ref(false)
  
  // 每個使用此 composable 的元件都有獨立的狀態實例
  return {
    users,
    loading
  }
}

// 混合模式 - 部分共享，部分獨立
const sharedUserCache = new Map<string, User>()

export const useUserWithCache = () => {
  const localUsers = ref<User[]>([])
  const loading = ref(false)
  
  const getUserFromCache = (id: string) => {
    return sharedUserCache.get(id)
  }
  
  const cacheUser = (user: User) => {
    sharedUserCache.set(user.id, user)
  }
  
  return {
    users: localUsers,
    loading,
    getUserFromCache,
    cacheUser
  }
}
```

### 模組化 Composable 設計

**主整合模組模式**：
```typescript
// composables/user/useUserApi.ts
export const useUserApi = () => {
  // API 相關邏輯
}

// composables/user/useUserState.ts  
export const useUserState = () => {
  // 狀態管理邏輯
}

// composables/user/useUserValidation.ts
export const useUserValidation = () => {
  // 表單驗證邏輯
}

// composables/user/useUserPermissions.ts
export const useUserPermissions = () => {
  // 權限檢查邏輯
}

// composables/user/index.ts - 主整合模組
export const useUserManagement = (options?: UserManagementOptions) => {
  // 整合所有子模組
  const api = useUserApi()
  const state = useUserState()
  const validation = useUserValidation()
  const permissions = useUserPermissions()
  
  // 統一對外介面
  const fetchUsers = async (params?: UserQueryParams) => {
    if (!permissions.canRead('users')) {
      throw new Error('沒有讀取使用者的權限')
    }
    
    state.setLoading(true)
    try {
      const users = await api.getUsers(params)
      state.setUsers(users)
      return users
    } finally {
      state.setLoading(false)
    }
  }
  
  const createUser = async (data: UserCreateRequest) => {
    if (!permissions.canCreate('users')) {
      throw new Error('沒有新增使用者的權限')
    }
    
    const validationResult = validation.validateCreateRequest(data)
    if (!validationResult.isValid) {
      throw new Error(validationResult.errors.join(', '))
    }
    
    const newUser = await api.createUser(data)
    state.addUser(newUser)
    return newUser
  }
  
  return {
    // 狀態
    users: state.users,
    loading: state.loading,
    
    // 操作方法
    fetchUsers,
    createUser,
    updateUser,
    deleteUser,
    
    // 工具方法
    validateUser: validation.validateUser,
    canManageUser: permissions.canManage
  }
}
```

### 錯誤處理和通知

**統一錯誤處理模式**：
```typescript
/**
 * 使用者管理 Composable 包含完整的錯誤處理
 */
export const useUserManagement = () => {
  const { showError, showSuccess } = useNotification()
  const { handleApiError } = useErrorHandler()
  
  const users = ref<User[]>([])
  const loading = ref(false)
  const error = ref<string | null>(null)
  
  /**
   * 安全的 API 呼叫包裝器
   */
  const safeApiCall = async <T>(
    apiCall: () => Promise<T>,
    options?: {
      loadingMessage?: string
      successMessage?: string
      errorMessage?: string
      showLoading?: boolean
    }
  ): Promise<T | null> => {
    const {
      loadingMessage = '處理中...',
      successMessage,
      errorMessage,
      showLoading = true
    } = options || {}
    
    try {
      if (showLoading) {
        loading.value = true
      }
      error.value = null
      
      const result = await apiCall()
      
      if (successMessage) {
        showSuccess(successMessage)
      }
      
      return result
    } catch (err) {
      const handledError = handleApiError(err)
      error.value = handledError.message
      
      showError(errorMessage || handledError.message)
      return null
    } finally {
      if (showLoading) {
        loading.value = false
      }
    }
  }
  
  const fetchUsers = async (params?: UserQueryParams) => {
    const result = await safeApiCall(
      () => getUserList(params),
      {
        successMessage: '使用者列表載入完成',
        errorMessage: '載入使用者列表失敗'
      }
    )
    
    if (result) {
      users.value = result.data || []
    }
  }
  
  const createUser = async (data: UserCreateRequest) => {
    const result = await safeApiCall(
      () => createUserApi(data),
      {
        successMessage: `使用者 ${data.name} 建立成功`,
        errorMessage: '建立使用者失敗'
      }
    )
    
    if (result) {
      users.value.push(result.data!)
    }
    
    return result
  }
  
  return {
    users: readonly(users),
    loading: readonly(loading),
    error: readonly(error),
    fetchUsers,
    createUser,
    // ... 其他方法
  }
}
```

### 生命週期和清理

**資源管理和清理**：
```typescript
export const useUserRealtime = () => {
  const users = ref<User[]>([])
  const connection = ref<WebSocket | null>(null)
  
  // 建立連線
  const connect = () => {
    connection.value = new WebSocket('ws://localhost:3000/users')
    
    connection.value.onmessage = (event) => {
      const data = JSON.parse(event.data)
      handleRealtimeUpdate(data)
    }
    
    connection.value.onerror = (error) => {
      console.error('WebSocket 錯誤:', error)
    }
  }
  
  // 處理即時更新
  const handleRealtimeUpdate = (data: RealtimeUserUpdate) => {
    switch (data.type) {
      case 'USER_CREATED':
        users.value.push(data.user)
        break
      case 'USER_UPDATED':
        const index = users.value.findIndex(u => u.id === data.user.id)
        if (index !== -1) {
          users.value[index] = data.user
        }
        break
      case 'USER_DELETED':
        users.value = users.value.filter(u => u.id !== data.userId)
        break
    }
  }
  
  // 清理資源
  const cleanup = () => {
    if (connection.value) {
      connection.value.close()
      connection.value = null
    }
  }
  
  // 自動清理（當元件卸載時）
  onUnmounted(() => {
    cleanup()
  })
  
  return {
    users: readonly(users),
    connect,
    cleanup
  }
}
```

### 測試友好的 Composable 設計

**可測試的 Composable 結構**：
```typescript
/**
 * 可測試的使用者管理 Composable
 * 依賴注入模式，便於單元測試
 */
interface UserApiDependencies {
  userApi: {
    getUsers: (params?: UserQueryParams) => Promise<User[]>
    createUser: (data: UserCreateRequest) => Promise<User>
    updateUser: (id: string, data: UserUpdateRequest) => Promise<User>
    deleteUser: (id: string) => Promise<void>
  }
  notificationService: {
    showSuccess: (message: string) => void
    showError: (message: string) => void
  }
}

export const useUserManagement = (dependencies?: Partial<UserApiDependencies>) => {
  // 預設依賴（生產環境）
  const deps = {
    userApi: {
      getUsers: getUserList,
      createUser: createUserApi,
      updateUser: updateUserApi,
      deleteUser: deleteUserApi
    },
    notificationService: {
      showSuccess: useNotification().showSuccess,
      showError: useNotification().showError
    },
    ...dependencies // 允許測試時注入 mock 依賴
  }
  
  const users = ref<User[]>([])
  const loading = ref(false)
  
  const fetchUsers = async (params?: UserQueryParams) => {
    loading.value = true
    try {
      const result = await deps.userApi.getUsers(params)
      users.value = result
      deps.notificationService.showSuccess('使用者列表載入完成')
    } catch (error) {
      deps.notificationService.showError('載入失敗')
      throw error
    } finally {
      loading.value = false
    }
  }
  
  return {
    users: readonly(users),
    loading: readonly(loading),
    fetchUsers
  }
}

// 測試範例
// test('useUserManagement should fetch users', async () => {
//   const mockApi = {
//     getUsers: vi.fn().mockResolvedValue([{ id: '1', name: 'Test' }])
//   }
//   const mockNotification = {
//     showSuccess: vi.fn(),
//     showError: vi.fn()
//   }
//   
//   const { fetchUsers, users } = useUserManagement({
//     userApi: mockApi,
//     notificationService: mockNotification
//   })
//   
//   await fetchUsers()
//   
//   expect(mockApi.getUsers).toHaveBeenCalled()
//   expect(users.value).toHaveLength(1)
//   expect(mockNotification.showSuccess).toHaveBeenCalledWith('使用者列表載入完成')
// })
```

---

**重要提醒**: 此完整模板包含所有必要的開發規範，專為 Claude Code 設計。使用時請嚴格按照計劃-執行模式進行，確保所有檢查項目都已完成。