# π 4th Seminar 

## π‘ μΈλ―Έλ λ΄μ©

- Retrofit2λ‘ μλ²μ ν΅μ νκΈ°



## π£ LEVEL1. μλ¦°μ΄ νμΆνκΈ°

### κ³Όμ 

- λ‘κ·ΈμΈ, νμκ°μ ν΅μ  κ΅¬ννκΈ°

### π· μ¬μ§, μμ

πνμκ°μ, λ‘κ·ΈμΈ μλ² ν΅μ 

<img width=300 alt="νμκ°μ, λ‘κ·ΈμΈ μλ² ν΅μ " src="https://user-images.githubusercontent.com/53166299/118315372-686d8e00-b530-11eb-86f6-7f2440a8a190.gif" style="float:left">

πνμκ°μ μ  λ‘κ·ΈμΈ PostMan
<img width="400" alt="νμκ°μ μ  λ‘κ·ΈμΈpostman" src="https://user-images.githubusercontent.com/53166299/118391617-2c414700-b670-11eb-8bf0-5cb34f3fbc18.png" >

πνμκ°μ ν λ‘κ·ΈμΈ PostMan
<img width="400" alt="νμκ°μ ν λ‘κ·ΈμΈ postman" src="https://user-images.githubusercontent.com/53166299/118391629-42e79e00-b670-11eb-9334-e748f9a3ea2f.png" >

πνμκ°μ ν νμκ°μ PostMan
<img width="400" alt="νμκ°μ ν νμκ°μ postman" src="https://user-images.githubusercontent.com/53166299/118391646-53981400-b670-11eb-804c-bb6df98ca044.png">



### μ½λ

- ServiceCreator - Retrofit Interface κ΅¬νμ²΄

  ```kotlin
  object ServiceCreator {
      private const val BASE_URL = "http://cherishserver.com"
  
      private val retrofit: Retrofit = Retrofit.Builder()
          .baseUrl(BASE_URL)
          .addConverterFactory(GsonConverterFactory.create())
          .build()
  
      val loginService: LoginService = retrofit.create(LoginService::class.java)
  }
  ```

- LoginService : μλ² μμ²­ λμ

  ```kotlin
  interface LoginService {
      @POST("/login/signin")
      fun postLogin(
          @Body body: RequestLoginData
      ) : Call<ResponseLoginData>
  
      @POST("/login/signup")
      fun postSignUp(
          @Body body: RequestSignUpData
      ) : Call<ResponseSignUpData>
  }
  ```

- μλ² Request, Response κ°μ²΄ μ€κ³

  ```kotlin
  //RequestLoginData.kt
  data class RequestLoginData (
      @SerializedName("email")
      val id: String,
      val password: String,
  )
  
  //RequestSignUpData.kt
  data class RequestSignUpData (
      @SerializedName("email")
      val id: String,
      val password: String,
      val sex: String,
      val nickname: String,
      val phone: String,
      val birth: String,
  )
  
  //ResponLoginData.kt
  data class ResponseLoginData (
      @SerializedName("email")
      val id: String,
      val password: String,
      val data: LoginData?
  )
  
  data class LoginData(
      @SerializedName("UserId")
      val userId: Int,
      val user_nickname: String,
      val token: String
  )
  
  //ResponSignUpData.kt
  data class ResponseSignUpData (
      @SerializedName("email")
      val id: String,
      val password: String,
      val sex: String,
      val nickname: String,
      val phone: String,
      val birth: String,
      val data: SignUpData?
  )
  
  data class SignUpData(
      val nickname: String
  )
  ```

- ν΅μ  μμ²­ - SignInActivity.kt : λ‘κ·ΈμΈ λ²νΌ ν΄λ¦­ μ λΉμΉΈμ΄ μλ€λ©΄ μλ² μμ²­

  ```kotlin
  private fun loginButtonClickEvent() {
          binding.btnLogin.setOnClickListener {
  
              val userId = binding.etLoginId.text
              val userPw = binding.etLoginPw.text
              if (userId.isNullOrBlank() || userPw.isNullOrBlank()) {
                  Toast.makeText(this@SignInActivity, "μμ΄λ/λΉλ°λ²νΈλ₯Ό νμΈν΄μ£ΌμΈμ!", Toast.LENGTH_SHORT)
                      .show()
              } else {
                  loginCommunicateServer()
              }
          }
      }
  
      private fun loginCommunicateServer(){
          //μλ²λ‘ λ³΄λΌ λ‘κ·ΈμΈ λ°μ΄ν° μμ±
          val requestLoginData = RequestLoginData(
              id = binding.etLoginId.text.toString(),
             password = binding.etLoginPw.text.toString()
          )
  
          val call: Call<ResponseLoginData> = ServiceCreator.loginService.postLogin((requestLoginData))
  
          call.enqueue(object: Callback<ResponseLoginData> {
              override fun onResponse(
                  call: Call<ResponseLoginData>,
                  response: Response<ResponseLoginData>
              ) {
                  if(response.isSuccessful){
                      val data = response.body()?.data
                      Toast.makeText(this@SignInActivity, "λ‘κ·ΈμΈ μ±κ³΅ \n userNickname : "+data?.user_nickname, Toast.LENGTH_SHORT).show()
  
                      startHomeActivity()
                  }else{
                      //μλ¬λ¬μ λ μ½λ
                      Toast.makeText(this@SignInActivity, "λ‘κ·ΈμΈ μ€ν¨", Toast.LENGTH_SHORT).show()
                  }
              }
  
              override fun onFailure(call: Call<ResponseLoginData>, t: Throwable) {
                  Log.d("NetworkTestSignIn","error:$t")
              }
          })
      }
  ```

  

  ν΅μ  μμ²­ - SignUpActivity.kt : νμκ°μ λ²νΌ ν΄λ¦­ μ λΉμΉΈμ΄ μλ€λ©΄ μλ² μμ²­

  ```kotlin
  private fun signUpButtonClickEvent() {
      binding.btnSignUp.setOnClickListener {
  ...
          } else {
              signUpCommunicateServer()
  
              val intent = Intent(this@SignUpActivity, HomeActivity::class.java)
              intent.putExtra("userName", userName.toString())
              .putExtra("userId", userId.toString())
              .putExtra("userPw", userPw.toString())
  
              setResult(
                  RESULT_OK,
                  intent
              )   
              finish()
          }
      }
  }
  
  private fun signUpCommunicateServer(){
      //μλ²λ‘ λ³΄λΌ νμκ°μ λ°μ΄ν° μμ±
      val requestSignUpData = RequestSignUpData(
          id = binding.etSignupId.text.toString(),
          password = binding.etSignupPw.text.toString(),
          sex = "0",
          nickname = binding.etSignupName.text.toString(),
          phone = "010-0000-0000",
          birth = "1999-00-00"
      )
  
      val call: Call<ResponseSignUpData> = ServiceCreator.loginService.postSignUp((requestSignUpData))
  
      call.enqueue(object: Callback<ResponseSignUpData> {
          override fun onResponse(
              call: Call<ResponseSignUpData>,
              response: Response<ResponseSignUpData>
          ) {
              if(response.isSuccessful){
                  val data = response.body()?.data
                  Toast.makeText(this@SignUpActivity, "νμκ°μ μ±κ³΅ \n μ΄λ¦ : "+data?.nickname, Toast.LENGTH_SHORT).show()
  
              }else{
                  //μλ¬λ¬μ λ μ½λ
                  Toast.makeText(this@SignUpActivity, "νμκ°μ μ€ν¨", Toast.LENGTH_SHORT).show()
              }
          }
  
          override fun onFailure(call: Call<ResponseSignUpData>, t: Throwable) {
              Log.d("NetworkTestSignUp","error:$t")
          }
      })
  }
  ```



### λ°°μ΄ λ΄μ©

- retrofit2 μ μ΄μ©ν μλ² ν΅μ  λ°©λ²

  1. μλ² Request, Response κ°μ²΄ μ€κ³ (data class)
  2. Retrofit Interface κ΅¬νμ²΄ λ§λ€κΈ° (ApiService, ServiceCreator)
  3. callbackμ λ±λ‘νλ©° ν΅μ  μμ²­νκΈ° (Activity, Fragment)

- recyclerview λ°μ΄ν°λ°μΈλ© μ¬μ© (repo, follow)

  - Item_repository.xml

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <layout xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">
  
      <data>
          <variable name="repoData" type="com.example.sopt_1.data.RepositoryInfo" />
      </data>
  ...
          <TextView
              android:id="@+id/tv_repository_name"
              ...
              android:text="@{repoData.repoName}"
              .../>
  
          <TextView
              android:id="@+id/tv_repository_info"
              ...
              android:text="@{repoData.repoInfo}"
              ... />
  
          <TextView
              android:id="@+id/tv_language"
              ...
              android:text="@{repoData.repoLanguage}"
              .../>
      </androidx.constraintlayout.widget.ConstraintLayout>
  </layout>
  ```

  - RepositoryListAdapter.kt

  ```kotlin
  ...
  override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RepositoryViewHolder {
          val binding = ItemRepositoryBinding.inflate(
              LayoutInflater.from(parent.context),
              parent,
              false
          )
          return RepositoryViewHolder(binding)
  ...
  class RepositoryViewHolder(
      private val binding: ItemRepositoryBinding
  ) : RecyclerView.ViewHolder(binding.root) {
      fun onBind(repositoryInfo: RepositoryInfo) {
          binding.repoData = repositoryInfo
      }
  }
  ```


# π 2nd Seminar 

## π‘ μΈλ―Έλ λ΄μ©

- RecyclerViewλ₯Ό μ¬μ©νμ¬ λ¦¬μ€νΈ λ§λ€κΈ°
- νλ‘μ νλ©΄ κ΅¬ν



## π£ LEVEL1. μλ¦°μ΄ νμΆνκΈ°

### κ³Όμ 

- ν νλ©΄μ User μ λ³΄μ more λ²νΌ μΆκ°νμ¬ νλ‘μ νλ©΄μΌλ‘ λμ΄κ°κΈ° νκΈ°
- ν νλ©΄μ λ ν¬μ§ν°λ¦¬ λ¦¬μ€νΈ κ΅¬ννκΈ° (RecyclerView)
  - λ ν¬μ§ν°λ¦¬ μ΄λ¦κ³Ό μ€λͺμ΄ κΈΈ κ²½μ° ... μΌλ‘ νμ



### κ΅¬ν λ°©λ²

1. Recyclerviewμ μ¬μ©λ  item layout λ§λ€κΈ° 

   - item_repository.xml

2. λ¦¬μ€νΈκ° λ³΄μ¬μ§ activiryμ Recyclerview μΆκ° 

   - activity_home.xmlμ μΆκ°ν¨

3. λ ν¬μ§ν°λ¦¬ λ¦¬μ€νΈμ λ€μ΄κ° λ΄μ©μ data class λ‘ λ§λ€κΈ°

   - RepositoryInfo.kt

4. μ΄λν° ν΄λμ€ λ§λ€κΈ°

   - RepositoryListAdapter.kt
   - RecyclerView.Adapter<RepositoryListAdapter.RepositoryViewHolder>() μμ
     - RepositoryListAdapter class μμ RepositoryViewHolder ν΄λμ€λ μμ± 
   - Adapterλ ViewHolderλ‘ λ³κ²½ν  Data κ°μ§κ³  μμ΄μΌ ν¨
     - ``` userList = mutableListOf<RepositoryInfo>() ```
   - Adapterλ μμ΄νλ§λ€ ViewHolderλ₯Ό λ§λλ λ°©λ² μ μ
   - Adapterλ μ μ²΄ μμ΄νμ μ μμμΌ ν¨
   - Adapterλ ViewHolderμ Data μ λ¬νλ λ°©λ² μ μ

5. Recyclerviewκ° λ€μ΄κ° νλ©΄μ Activity/Fragmentμ μ΄λν° μΆκ°

   - HomeActivity.kt

   - ``` kotlin
     // 1. μ°λ¦¬κ° μ¬μ©ν  μ΄λν°μ μ΄κΈ° κ°μ λ£μ΄μ€λ€
             repositoryListAdapter =
                 RepositoryListAdapter()
     
             // 2. RecyclerView μ μ΄λν°λ₯Ό μ°λ¦¬κ° λ§λ  μ΄λν°λ‘ λ§λ€κΈ°
             binding.listRepository.adapter = repositoryListAdapter
     
             repositoryListAdapter.userList.addAll(
                 listOf<RepositoryInfo>(
                     RepositoryInfo(
                         repoName = "And-Potter/Ravenclaw_HJinhee",
                         repoInfo = "μ§ν¬μ μνΈ λ ν¬μ§ν°λ¦¬",
                         repoLanguage = "Kotlin"
                     ),
                     RepositoryInfo(
                         repoName = "λ ν¬μ§ν°λ¦¬ μ΄λ¦μ΄ μμ²­ κΈΈ λ ... νμλλλ‘ νλκ±° λ³΄μ¬μ£Όλ €κ³  μ΄λ κ² κΈΈκ² μλ³΄λ μ€μλλ€μ",
                         repoInfo = "λ ν¬μ§ν°λ¦¬ μ€λͺμ΄ μμ²­ κΈΈ λ ... νμλλλ‘ νλκ±° λ³΄μ¬μ£Όλ €κ³  μ΄λ κ² κΈΈκ² μλ³΄λ μ€μλλ€μ",
                         repoLanguage = "Java"
                     )
                 )
             )
     
             repositoryListAdapter.notifyDataSetChanged()
     ```
     
     
# π 1st Seminar Seminar

## π£ LEVEL1. μλ¦°μ΄ νμΆνκΈ°
- signIn, signUp, home νλ©΄ κ΅¬ν

## π£ LEVEL12.
- activity_sign_in.xmlμμ Guidline νμ©
- activity_home.xmlμμ μκΈ°μκ° ScrollView νμ©
 

## 1. νλ©΄ μ ν ν λ°μ΄ν°λ₯Ό κ°μ Έμ€λ λ‘μ§
SignInActivity.kt
```
private val signUpActivityLauncher =
        registerForActivityResult(ActivityResultContracts.StartActivityForResult()) {
            //λ°μ΄ν°λ₯Ό λ°μμ ν  μΌμ΄ λ€μ΄κ°λ μΉΈ
            if (it.resultCode == Activity.RESULT_OK) {
                if (intent != null) {
                    binding.etLoginId.setText(it.data?.extras?.getString("userId"))
                    binding.etLoginPw.setText(it.data?.extras?.getString("userPw"))
                }
            }

        }
 ```
 ```
 private fun signUpClickEvent() {
        binding.tvSignUp.setOnClickListener {

            val intent = Intent(this@SignInActivity, SignUpActivity::class.java)
            signUpActivityLauncher.launch(intent)

```

 
SignUpActivity.kt
 ```
private fun signUpButtonClickEvent() {
        binding.btnSignUp.setOnClickListener {

            ...

            } else {
                //μ΄κΈ° SignUpActivityλ‘ λμκ° μ μλλ‘ μ’λ£
                // μ’λ£ μ  putExtraλ₯Ό μ΄μ©ν΄ λͺ¨λ  κ°μ intentμ λ£μ΄ μ λ¬
                val intent = Intent(this@SignUpActivity, HomeActivity::class.java)
                intent.putExtra("userName", userName.toString())
                    .putExtra("userId", userId.toString())
                    .putExtra("userPw", userPw.toString())

                setResult(
                    RESULT_OK,
                    intent
                )   //setResult() λ©μλλ‘ κ²°κ³Όλ₯Ό μ μ₯ -> μ±κ³΅ : RESULT_OK, μ€ν¨ : RESULT_CANCEL
                finish()
            }
        ...
 ```


## 2. μλͺμ£ΌκΈ° νΈμΆ λ‘κ·Έ
![image](https://user-images.githubusercontent.com/53166299/114308901-58712180-9b20-11eb-90b7-9a7bed4971a0.png)



## 3. κΈ°ν
### νλ©΄μ ν _ startActivityForResult()
##### startActivityForResult() - νλ©΄μ ν + κ²°κ³Όκ° λ°ν
##### setResult() λ©μλλ‘ κ²°κ³Ό μ μ₯
##### finish() λ‘ μ΄κΈ° νλ©΄μΌλ‘ λμκ°κΈ°
##### onActivityResult() λ©μλμμ μ¬λ¬ κ°μ startActivityForResult() κ΅¬λΆ κ°λ₯ / νΈμΆλ Activityμμ μ μ₯ν κ²°κ³Όλ₯Ό λλ €μ€
![start 1](https://user-images.githubusercontent.com/53166299/114663747-c0e51c00-9d35-11eb-96c3-a42116302ac1.png)
![start 2](https://user-images.githubusercontent.com/53166299/114663755-c3e00c80-9d35-11eb-9b3a-4eec441872b6.png)
![start 3](https://user-images.githubusercontent.com/53166299/114663764-c6426680-9d35-11eb-9983-0f7af93c3de7.png)



-> μ΄ κΈ°λ₯μ΄ μ¬λΌμ§λ©΄μ registerForActivityResult()μμ μ²λ¦¬ κ°λ₯

### νλ©΄μ ν_ registerForActivityResult()
##### registerForActivityResult() λ©μλλ₯Ό ν΅ν΄ ActivityLauncher λ§λ€κΈ°
##### -> μ¬κΈ°μ λ°λ‘ λ°μ΄ν° λΆλ¬μ€κ³  μ¬μ© κ°λ₯
##### ActivityLauncher λ₯Ό launch ν  λ intentλ₯Ό μΈμλ‘ μ λ¬
##### -> μ΄λ€ μ‘ν°λΉν°μμ μμ²­μ λ³΄λλμ§ νμΈν  νμμμ΄, ν΄λΉ μ‘ν°λΉν°μμ λ³΄λΈ μμ²­μ λ¬΄μ‘°κ±΄ registerForActivityResult()μ collbackμΌλ‘ λ¨μ΄μ§
##### setResult() , finish()λ μ΄μ κ³Ό λμΌ





