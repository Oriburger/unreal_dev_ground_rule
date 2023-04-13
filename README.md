# unreal_dev_ground_rule
- This is code-level rule of unreal project for new ue team. 
- 2022/06/26 13:51 최초 작성) [원문 - DreamBox 그라운드 룰](https://oriburger.notion.site/DreamBox-6f888b4121644753998eaf3befdefc61)


## 기본

- 클래스 체계
    - `public` 선언 → 이후 `private` 구현
    - 선언과 정의는 각각의 파일에 분리 (`.h`, `.cpp`)
- 중괄호
    - BSD 스타일로 통일
    
    ```cpp
    void UpdateCharacterHPValue()
    **{**
       ...
    **}**
    ```
    
- 작명 기본
    - 기본적으로 **PascalCase**를 따름
        
        ```cpp
        public class AMainCharacter{...}
        int32 PlayerIndex;
        float CharacterHP;
        
        FCharacterStat GetCharacterStat(ACharacterBase* CharacterRef);
        ```
        
    - 단, 블록 내 임시 변수에는 camelCase를 사용
        
        ```
        {
        		APlayerCharacter * tempCharacter = GetPlayer(..);
        		...
        }
        ```
        
- Circular Dependency
    - 헤더파일에는 최소한의 라이브러리만 include
        - 대부분은 전방 선언을 통해 진행

## Reflection — ([참고](https://www.notion.so/6f888b4121644753998eaf3befdefc61))

---

- UPROPERTY의 Category는 분류에 따라 작성하기
    
    ```cpp
    ...
    UPROPERTY(EditAnywhere, Category = "Gameplay")
    	float CharacterHP = 100.0f;
    
    UPROPERTY(EditAnywhere, Category = "VFX")
    	UParticleSystem * ExplosionParticleSystem; 
    ...
    ```
    
- UFUNCTION, UPROPERTY의 공개범위, 수정권한
    - BP 파생 클래스에서 Read/Write가 없는 코드는 BP에 권한을 주지 않기
    - BP 파생 클래스의 디테일 패널에 보여야하는 부분만 공개하기

## C++ 작명규칙

---

- 기본적으로 **PascalCase**를 따름
    
    ```cpp
    public class AMainCharacter{...}
    int32 PlayerIndex;
    float CharacterHP;
    ```
    
- 단, 블록 내 임시 변수에는 CamelCase를 사용
    
    ```
    {
    		APlayerCharacter * tempCharacter = GetPlayer(..);
    		...
    }
    ```
    
- **접두사**
    - 변수 이름과 구분하기 위해 대문자 하나로 나타내는 접두사를 붙임
    1. 클래스
        - `UObject`의 하위 클래스에 붙는 접두사 **U**
        - `AActor`의 하위 클래스에 붙는 접두사 **A**
        - `SWidget`의 하위 클래스 접두사는 **S**
        - `추상 인터페이스`의 접두사는 **I** (대문자 i)
        - 그 외 클래스는 대부분p **F**
    2. 열거형
        - Enum의 접두사는 **E**
    3. boolean
        - 접두사 **b** :  `bIsDestroying`
    4. Typedef
        - 실제 타입의 접두사를 따름
        - 예시 : `typedef TArray<FMytype> FArrayOfMyTypes;`
- 변수명
    - 명사 형태로 작성
    - 파라미터나 반환값이 `bool`이라면 접두사 **b**를 붙임
- 함수명
    - 주로 동사로 작성하나, 반환값을 설명할 수도 있음
    - 반환 타입이 `bool`이라면, 질문 형태로 작성
        
        → 예시 : `IsVisible()` , `ShouldDestroyProjectile()`
        
- 종합 예시 (UE4 Docs)
    
    ```cpp
    float TeaWeight;
    int32 TeaCount;
    bool bDoesTeaStink;
    FName TeaName;
    FString TeaFriendlyName;
    UClass* TeaClass;
    USoundCue* TeaSound;
    UTexture* TeaTexture;
    ```
    

## Blueprint 작명 규칙

---

- 기본적으로 내부 이벤트, 함수, 변수 등의 작명 규칙은 C++과 동일
- C++ 클래스 파생 BP는 앞에 `BP_`를 붙임
    - 그 외에도 사용자 임의 BP는 모두 포함 (Widget, AnimBP etc..)
- 이외의 파일은 유형에 따라 접두사를 붙임
    
    ```cpp
    - Niagara System : **NS**
    - Particle System : **PS**
    - Animation Montage : **AM**
    - Struct : **S**
    - Enum : **E**
    - DataSheet : **D**
    - Widget : W
    - Font : F, FF
    **...** 
    ```
    
    ***+) 애매한 것은 바로 Discord이나 Trello에 올려주세요!!!!!***
    

## 주석

---

- 주석은 ‘한글로' 작성
- 클래스
    - 각 클래스별 주석은 `/**/`로, 아래 양식에 맞게 작성
    
    ```cpp
    /*
     - Name        : AFire
     - Descirption : FireHose의 물에 닿으면 꺼지는 불 액터
     - Date        : 2022/06/22 LJH
    */
    
    UCLASS(Category = "FireFighter")
    class LISTENSERVERSTUDY_API AFire : public AActor
    {
    ```
    
- 함수 선언부
    - 헤더파일의 선언부에만 작성
    - 위에 한줄 주석`//`을 작성
        
        ```cpp
        //장애물의 위치를 특정 초 단위로 업데이트 한다. 
        void AObstacleBase::UpdateObstacleLocation()
        { . . . }
        ```
        
    - 반환형과 파라미터에 대한 설명도 포함
        
        ```cpp
        // 캐릭터의 방어력,, 을 기반으로 실제 받는 데미지를 계산해서 반환
        // Damage : ~~
        // FDamageEvent : ~~
        // DamageCause : ~~
        float AMainCharacter::GetDamage(float Damage, struct FDamageEvent, AActor* DamageCauser)
        {
        	...
        	return FinalDamage;
        }
        ```
        
- 함수 정의부
    - 각 라인에 맞게 간결히 작성
    - 라인이 긴 부분은 위에 한 줄 주석으로 작성
        
        ```cpp
        ...
        void AMainCharacter::StartSlide()
        {
          //캐릭터가 ~ ,, 하면 강재 종료
        	if (GetCharacterMovement()->IsCrouching() || bIsDead || bIsRagdoll || !bCanSlide) return;
        
        	StopJump(); 
          StopAim(); //슬라이드를 위해 점프와 조준을 취소
        	GetCharacterMovement()->MaxWalkSpeedCrouched = CharacterWalkSpeed;
        
        	//UE_LOG(LogTemp, Warning, TEXT("Crouch"));
        	ACharacter::Crouch();
        }
        ```
        

## 조건문과 반복문

---

- **조건문**
    - 한 줄을 제외한 모든 경우에 중괄호를 붙임
    - 들여쓰기는 반드시 지키기
    
    ```cpp
    if(IsCharacaterDead()) return; 
    else 
    {
    	 . . .
    }
    ```
    
- **반복문**
    - 단순 idx 접근을 제외하면, 변수명 `i`, `j`, `k`는 지양
    - 들여쓰기 필수
    - [auto, 범위 기반 반복문](https://www.notion.so/6f888b4121644753998eaf3befdefc61)
- Switch
    - 들여쓰기 필수
    - `defaults`에도 내용 기입 (예외처리문 등)

## 그 외

---

- **C++ 최신 문법**
    - `auto`의 사용을 금함
    - NULL을 `nullptr`로 대체
    - 범위 기반 반복문 허용

## 파일 관리

---

- 디렉토리 구조
    - `/Source`
        - 디렉토리 구조
            
            ```
            - Source/MyProject
               - private
               - public
              
            ```
            
            - public 폴더 내 헤더파일(.h)
            - private 폴더 내 소스파일(.cpp)
    - `/Content`
        - 디렉토리 구조
            
            ```
            - Content
               - Actor
               - Character
                   - Animation
               - Common
               - Map
               - StaticMesh
               - UI
               - Asset      //모든 외부 에셋이 포함
                  - StarterContent
                  - ...
            ```
            
        - 각 직업별 폴더 내 **사용자 정의 `.uasset`**를 다음과 같이 분류
            
            ```
            1. Actor - 사용자 정의 Actor (주로 prop)
            2. Character - NPC와 PlayerCharacter
            3. Effect - Particle, Niagara 등
            4. Sound - 사운드 관련 모든 파일 (원본 파일 미포함)
            5. Material - 머티리얼과 텍스쳐 
            6. Map - 레벨
            7. UI - 위젯BP
            8. Data - 데이터 파일(.csv 등), 구조체, 열거형
            ```
            
            *→ 단, 사용자가 외부에서 받는 100mb 이상의 파일(3D 모델, 사운드 등)은 
                 에셋으로 포함. `./Content/Asset/직업명` 디렉토리에 별도로 분류*
            
            - `*_BuiltData.uasset`의 경우는 별도로 로컬에 관리
        - Common 디렉토리 내에는 공통으로 사용하는 요소
            - 주로 GameInstance, Lobby레벨 등
    - 에셋 옮기는 법?
        - 반드시 에셋을 사용하지 않은 상태에서 진행
        - 우선, 폴더 째로 이동을 누름
        - 그리고 폴더가 남아있다면 우클릭 - 폴더 리디렉터 고치기 클릭
