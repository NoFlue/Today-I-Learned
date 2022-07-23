# Room
`Room DataBase` 는 `SQLite` 를 활용한 로컬 DB 입니다. 네트워크에 액세스를 할 수 없을 때에도 오프라인에서 접근할 수 있다는 장점이 있습니다.  
<br>
SQLite 대신 Room을 사용함으로 얻는 이점은 다음과 같습니다.
- SQL 쿼리의 컴파일 시간 확인
- 반복적이고 오류가 발생하기 쉬운 상용구 코드를 최소화하는 편의 주석
- 간소화된 데이터베이스 이전 경로

<br>

### 설정
앱에서 Room을 사용하기 위해선 아래의 종속 항목을 추가하면 됩니다.
```groovy
dependencies{
	implementation "androidx.room:room-runtime:$room_version"
	annotationProcessor "androidx.room:room-compiler:$room_version"
}
```

<br>

### 기본 구성요소
Room에는 다음과 같은 구성요소로 이루어져 있습니다.
- `데이터 항목` : 앱 데이터베이스의 테이블을 나타냅니다.
- `DAO(Data Access Objects)` : 앱이 데이터베이스의 데이터를 쿼리, 업데이트, 삽입, 삭제하는 데 사용할 수 있는 메서드를 제공합니다.
- `데이터베이스 클래스` : 데이터베이스를 보유하고 앱의 영구 데이터와의 기본 연결을 위한 기본 액세스 포인트 역할을 합니다.

`데이터베이스 클래스는` 는 데이터베이스와 연결된 `DAO 인스턴스를` 를 앱에 제공합니다. 앱은 연결된 `DAO` 를 사용하여 데이터베이스의 데이터를 연결된 `데이터 항목` 객체의 인스턴스로 검색할 수 있습니다. 앱은 정의된 `데이터 항목` 을 사용하여 상응하는 테이블의 행을 업데이트하거나 삽입할 새 행을 만들 수도 있습니다.

<p align="center">
	<img src="https://developer.android.com/static/images/training/data-storage/room_architecture.png?hl=ko">
</p>
<p align="center">
	Room 구성요소 간 관계 [출처 : developer.android.com]
</p>

<br>
<br>
<br>

# 데이터 항목
Room 데이터 모델은 `@Entity` 주석이 달린 클래스로 정의해야 합니다.  
Room 데이터는 `기본 키` 와 테이블에 있는 각 열의 필드가 포함되어 있습니다.
```kotlin
@Entity
data class User(
	@PrimaryKey val id: Int = 0,
	val firstName: String?,
	val lastName: String?
)
```
<br>

기본적으로 데이터 클래스 이름을 테이블 이름으로 사용하지만 `tableName` 속성을 설정하면 다른 이름으로 사용 가능합니다.  
열 또한 필드 이름으로 사용하며 `@ColumnInfo` 주석을 필드에 추가하고 `name` 속성을 설정하면 다른 이름으로 사용 가능합니다.
```kotlin
@Entity(tableName = "users")
data class User(
	@PrimaryKey val id: Int = 0,
	@ColumnInfo(name = "first_name") val firstName: String?,
	@ColumnInfo(name = "last_name") val lastName: String?
)
```
> `SQLite` 의 테이블 및 열 이름은 **대소문자를 구분하지 않습니다**

<br>

기본 키는 `@PrimaryKey` 주석을 필드에 추가하여 지정할 수 있으며 `autoGenerate` 속성을 `true` 로 설정하면 자동으로 `ID` 를 할당합니다.
```kotlin
@Entity
data class User(
	@PrimaryKey(autoGenerate = true) val id: Int = 0,
	val firstName: String?,
	val lastName: String?
)
```

<br>
<br>
<br>

# DAO를 이용하여 데이터 액세스
Room을 사용하여 앱 데이터 저장을 할 때 `DAO`를 정의하여 저장된 데이터와 상호작용합니다.  
<br>
`DAO(Data Access Objects)` 는 인터페이스나 추상 클래스로 정의할 수 있으며, 일반적으로 인터페이스를 사용합니다.  
`@Dao` 주석을 인터페이스에 추가해야하며, 앱 데이터와 상호작용할 수 있는 메서드 하나 이상을 정의해야합니다.
```kotlin
@Dao
interface UserDao{
	@Insert
	fun insertAll(users: List<User>)

	@Delete
	fun delete(user: User)

	@Query("SELECT * FROM user")
	fun getAll(): List<User>
}
```
DAO의 메서드의 정의는 두 가지 유형이 있습니다.
- `편의 메서드` : 쿼리 작성없이 데이터베이스를 관리할 수 있습니다.
- `쿼리 메서드` : 쿼리를 작성하여 데이터베이스와 상호작용할 수 있습니다.

<br>
<br>

## 편의 메서드
### 삽입
`@Insert` 주석을 추가하여 테이블에 데이터를 추가합니다.
```kotlin
@Dao
interface UserDao{
	@Insert
	fun insertUser(user: User)
```
메서드의 각 매개변수는 `@Entity` 주석이 달린 클래스의 인스턴스이거나 인스턴스의 컬렉션이어야 합니다.  

<br>

### 삭제
`@Delete` 주석을 추가하여 테이블에 동일한 아이디의 데이터가 있을 경우 데이터를 삭제합니다.
```kotlin
@Dao
interface UserDao{
	@Delete
	fun deleteUser(user: User)
}
```
Insert와 마찬가지로 메서드의 각 매개변수는 `@Entity` 주석이 달린 클래스의 인스턴스이거나 인스턴스의 컬렉션이어야 합니다. 

<br>

### 업데이트
`@Update` 주석을 추가하여 테이블에 동일한 아이디의 데이터가 있을 경우 데이터를 업데이트합니다.
```kotlin
@Dao
interface UserDao{
	@Update
	fun updateUser(user: User)
}
```

<br>
<br>

## 쿼리 메서드
`@Query` 주석을 사용해 쿼리문을 작성하면 좀 더 복잡한 처리가 가능합니다.  
쿼리문은 컴파일 시간에 검증하므로 에러가 날 경우 컴파일 에러가 발생합니다.
```kotlin
@Dao
interface UserDao{
	Query("SELECT * FROM user")
	fun getUsers(): List<User>

	@Query("SELECT * FROM user WHERE age > :minAge")
	fun getUsersOlderThan(minAge: Int): List<User>

	@Query("DELETE FROM user WHERE firstname = :firstName")
	fun deleteUserByFirstName(firstName: String)
}
```
> 아직 쿼리문에 익숙하지 않기 때문에 SQL을 배운 후 다양한 예제를 써보도록 하자

<br>
<br>
<br>

# 데이터베이스
데이터베이스를 보유할 `AppDatabase` 클래스를 정의 해야합니다. `AppDatabase` 는 데이터베이스를 생성하고 관리하는 역할을 합니다.  데이터베이스 클래스는 다음 조건을 만족해야 합니다.
- `@Entity` 주석이 달린 데이터 항목을 모두 나열하는 `entities` 배열이 포함된 `@Database` 주석이 달려야 합니다.
- `RoomDatabase` 클래스를 상속하는 추상 클래스여야 합니다.
- 데이터베이스와 연결된 각 DAO 클래스에서 데이터베이스 클래스는 인수가 0개이고 DAO 클래스의 인스턴스를 반환하는 추상 메서드를 정의해야 합니다.
```kotlin
@Database(entities = [User::class], version = 1)
abstract class UserDatabase: RoomDatabase(){
	abstract fun userDao(): UserDao
}
```
<br>

구글에서 `RoomDatabase` 인스턴스는 자원을 상당히 많이 소비하기 때문에 데이터베이스를 인스턴스화할 때 싱글턴 디자인 패턴을 따르는 걸 추천합니다.
```kotlin
@Database(entities = [User::class], version = 1)
abstract class UserDatabase: RoomDatabase(){
	abstract fun userDao(): UserDao

	companion object{
		private var instance: UserDatabase? = null

		@Syncronized
		fun getInstance(context: Context): UserDatabase?{
			if(instance == null){
				syncronized(UserDatabase::class){
					instance = Room.databaseBuilder(
						context.applicationContext,
						UserDatabase::class.java,
						"user-database"
					).build()
				}
			}
			return instance
		}	 
	}
}
```

<br>
<br>
<br>

# Room 데이터 사용
```kotlin
val user = User("No", "Flue")

UserDatabase.getInstance(applicationContext).insertUser(user)
```
하지만 이대로 사용하면 다음과 같은 에러가 발생한다.
> Cannot access database on the main thread since it may potentially lock the UI for a long period of time

메인 쓰레드에서 데이터베이스를 액세스 하지 못하기 때문에 비동기 처리를 해야하지만 `allowMainThreadQueries()` 메서드를 사용해 강제로 메인 쓰레드에 돌릴 수 있습니다.
```kotlin
val db = Room.databaseBuilder(
		applicationContext,
		UserDatabase::class.java,
		"user-database"
	).allowMainThreadQueries().build()

val user = User("No", "Flue")

db.UserDao().insertUser(user)
```

> 아직 `Coroutine` 을 배우지 않았기 때문에 코루틴을 이용한 비동기 처리는 나중에 추가하자