# Glide
## Glide는?
* 구글에서 공개한 이미지 라이브러리입니다.
* 웹 상의 이미지를 로드하여 보여주기 위해 고려해야 할 사항들을 미리 구현하여, 사용자가 이용하기 쉽게 만든 라이브러리입니다.

## Dependency 추가
~~~gradle
dependencies{
    implementation 'com.github.bumptech.glide:glide:4.8.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.8.0'
}
~~~

## 기본적인 이미지 로딩 사용법
* Glide 클래스는 빌더 패턴으로 구현되어 있고, 3개의 필수 파라미터를 요구합니다.
    * with(Context context) : 안드로이드의 많은 API를 이용하기 위해 필요
    * load(String imageUrl) : 웹 상에서의 이미지 경로 URL or 안드로이드 리소스 ID or 로컬 파일 or URI
    * into(ImageView targetImageView) : 다운로드 받은 이미지를 보여줄 이미지 뷰

~~~kotlin
// 웹 URL
val target: ImageView = findViewById(R.id.imageview)
val url = "http://www.example.com/icon.png"

Glide.with(context)
     .load(url)
     .into(target)


// 리소스 ID
val resourceId: Int = R.mipmap.ic_launcher

Glide.with(context)
     .load(resourceId)
     .into(target)


// 로컬 파일
val file = File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES), "Example.jpg")

Glide.with(context)
     .load(file)
     .into(target)


// URI
val uri = Uri.parse("android.resource://com.example.test/resource")

Glide.with(context)
     .load(uri)
     .into(target)
~~~
* 만일 해당 경로에 이미지가 없다면, Glide는 error 콜백을 리턴할 것입니다.

## PlaceHolder 이미지
### PlaceHolder
* PlaceHolder : 원본이미지를 보여주기 전에 잠깐 보여주는 이미지입니다
* 네트워크 로드 등, 이미지 로드에 시간이 오래 걸릴 때 빈화면 대신 PlaceHolder 이미지를 보여줍니다.
* Glide는 PlaceHolder 이미지를 리소스 영역에서 불러옵니다.

~~~kotlin
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .placeholder(R.mipmap.ic_launcher)
     .into(target)
~~~

### Error PlaceHolder
* 이미지 로드에 실패했을 때 등, 예상하지 못한 상황으로 원본이미지를 로드할 수 없을 때 보여주는 이미지입니다.
~~~kotlin
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .placeholder(R.mipmap.ic_launcher)
     .erro(R.mipmap.ic_error)        //Error상황에서 보여집니다.
     .into(target)
~~~

### Animation
* 원본 이미지가 다 로드되고 나면 PlaceHolder 이미지가 원본 이미지로 교체되는데, 이 때 애니메이션 처리를 할 수 있습니다.
* 이미지 교체 애니메이션은 기본 동작합니다.
* 애니메이션을 수동으로 On / Off 하려면 .crossFade() / dontAnimate를 호출합니다.
~~~kotlin
// Animation On
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .placeholder(R.mipmap.ic_launcher)
     .erro(R.mipmap.ic_error)        //Error상황에서 보여집니다.
     .crossFade()
     .into(target)


// Animation Off
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .placeholder(R.mipmap.ic_launcher)
     .erro(R.mipmap.ic_error)        //Error상황에서 보여집니다.
     .dontAnimate()
     .into(target)
~~~

## 이미지 리사이징하기
### 리사이징
* Glide는 기본적으로 이미지뷰의 사이즈에 맞게 이미지가 다운로드되고 캐싱됩니다.
* 명시적으로 이미지 사이즈를 변경하려면 override(x,y) 메서드를 호출합니다.
* 타켓 이미지뷰가 없을때, 미리 이미지를 특정 사이즈로 로드하는 용도로 사용됩니다.
~~~kotlin
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .override(600,200)
     .into(target)
~~~
### 스케일링
* Glide는 실제 이미지의 사이즈와 화면에 보이는 크기가 다를 때, 스케일링할 수 있는 옵션을 제공합니다.

* CenterCrop
* 실제 이미지가 이미지뷰의 사이즈보다 클 때, 이미지뷰의 크기에 맞춰 이미지 중간부분을 잘라서 스케일링합니다.
~~~kotlin
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .override(600,200)
     .centerCrop()
     .into(target)
~~~
* fitCenter
* 실제 이미지가 이미지뷰의 사이즈와 다를 때, 이미지와 이미지뷰의 중간을 맞춰서 이미지 크기를 스케일링합니다.
~~~kotlin
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .override(600,200)
     .fitCenter()
     .into(target)
~~~

## 이미지 캐싱
### 캐싱의 기본 정책
* Glide는 기본적으로 메모리 & 디스크에 이미지를 캐싱하여 불필요한 네트워크 연결을 줄입니다.

### 메모리 캐시
* 기본적으로 메모리 캐싱을 하기때문에, 메모리 캐싱을 위해 추가적으로 할 일은 없습니다.
* 메모리 캐싱을 끄려면 skipMemoryCache(true)를 호출합니다.
* 처음 메모리 캐싱을 한 후에, skipMemoryCache(true)로 캐싱을 중지하더라도, 그 전에 저장된 캐시는 그대로 남아있습니다.
~~~kotlin
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .skipMemoryCache(true)
     .into(target)
~~~

### 디스크 캐시
* 기본적인 개념은 메모리 캐시와 같다. Glide는 기본적으로 디스크 캐싱을 수행합니다.
* 디스크 캐싱을 끄려면 diskCacheStrategy(DiskCacheStrategy.NONE) 메서드를 호출합니다.
* diskCacheStrategy 메서드는 DiskCacheStrategy enum을 인수로 받습니다.
* DiskCacheStrategy.NONE : 디스크 캐싱을 하지 않습니다.
* DiskCacheStrategy.SOURCE : 원본 이미지만 캐싱
* DiskCacheStrategy.RESULT : 변형된 이미지만 캐싱
* DiskCacheStrategy.ALL : 모든 이미지를 캐싱(기본)
* 메모리 캐싱과는 별개이므로, 둘다 사용하지 않을 경우 다음과 같이 둘다 꺼주어야 합니다.
~~~kotlin
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .skipMemoryCache(true)
     .diskCacheStrategy(DiskCacheStrategy.NONE)
     .into(target)
~~~

## 이미지 로드 우선순위
### Priority
* Glide 라이브러리는 동시에 이미지 로드 명령을 받았을 때, 지정한 우선순위에 따라 이미지를 로드하도록 지원합니다.
* priority()메서드에 Priority열거형 타입을 인수로 지정하여 우선순위를 변경합니다.
* Priority.LOW
* Priority.NORMAL
* Priority.HIGH
* Priority.IMMEDIATE
* Glide 라이브러리는 이 우선순위대로 이미지 로드를 수행하지만, 반드시 우선순위 순서대로 진행된다는 보장은 없습니다.
~~~kotlin
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .priority(Priority.HIGH)
     .into(target)
~~~
## 썸네일
### 원본 썸네일
* 원본 이미지를 썸네일로 사용합니다.
* thumbnail()메서드를 이용합니다. 이 때, 크기의 배수값을 줌으로써 썸네일의 크기를 지정할 수 있습니다.
~~~kotlin
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .thumbnail(0.1f)
     .into(target)
~~~
* 원본 이미지를 사용하는 방법이기 때문에 원본이미지를 변경하면 썸네일에도 변경이 적용됩니다.

### 별도 썸네일
* 위의 방법과는 다르게, 원본과 썸네일 이미지를 각각 로드하는 방법입니다.
* 이때도 thumbnail()메서드를 이용합니다. 대신 썸네일을 위한 새로운 Request를 생성하여 인자로 주어야 합니다.
~~~kotlin
// into() 메서드를 뺀 Glide Request를 생성합니다.
DrawableRequestBuilder<String> thumbnailRequest = Glide
    .with(context)
    .load("http://www.example.com/thumbnail.png")

// 생성한 Request를 thumbnail() 메서드의 인자로 넣어줍니다.
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .thumbnail(thumbnailRequest)
     .into(target)
~~~
* 원본 이미지와는 별개의 리소스이므로 원본 이미지를 변경해도 썸네일은 변화가 없습니다.

## Target
* Glide는 기본적으로 이미지를 비동기로 로드하여 이미지뷰에 보여줍니다.
* 하지만, Target을 이용하면 이미지뷰에 보여주는 동작이 아닌 Bitmap 자체를 얻어오는 등, 여러 동작을 수행할 수 있습니다.
* Glide 입장에서는 일종의 콜백이라고 볼 수 있습니다.
* Target을 구현하는 방법은 BaseTarget 추상클래스를 상속받거나, SimpleTarget을 이용합니다.
### SimpleTarget
* SimpleTarget은 BaseTarget 클래스를 상속받은 클래스로 기본 동작이 구현되어 있고, onResourceReady메서드만 추가로 구현하면 됩니다.
* 리소스 로드가 완료되면 onResourceReady 메서드가 호출되므로, 이 메서드 내부에서 수행할 동작을 구성하면 됩니다.
~~~kotlin
// 로드된 이미지를 받을 Target을 생성합니다.
private val target = SimpleTarget<Bitmap>() {
    override fun onRersourceReady(bitmap: Bitmap, glideAnimation: GlideAnimation) {
        //TODO:: 리소스 로드가 끝난 후 수행할 작업
    }
}

// 생성한 Target을 into() 메서드의 인자로 넣어줍니다.
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .asBitmap()        // 리소스를 Btimap으로 강제하기 위해
     .into(target)
~~~

### Target을 구현할 때 고려사항
* Target 으로 쓸 인스턴스가 가비지컬렉션의 대상이 되지 않도록 주의해야 합니다. Target 인스턴스가 가비지컬렉션 되면 콜백을 받을 수 없습니다.
* 엑티비티의 생명주기와는 무관한 Target일 경우, 항상 Application Context를 이용합니다.

### 특정 크기를 지닌 Target
* Glide는 .into()에 이미지뷰를 넘기면, 이미지뷰의 크기를 고려하여, 그 크기에 맞게 이미지를 로드합니다.
* 이와 유사하게, Target을 생성할 때, 로드될 이미지의 크기를 지정하면, Glide는 이미지를 로드할 때, 그 크기를 참조하여 이미지를 해당 크기에 맞게 로드합니다.
~~~kotlin
// 로드된 이미지를 받을 Target을 생성합니다. 생성할 때, 크기를 지정해줍니다.
private var target = SimpleTarget<Bitmap>(250, 250) {
    override fun onRersourceReady(bitmap: Bitmap, glideAnimation: GlideAnimation) {
        //TODO:: 리소스 로드가 끝난 후 수행할 작업
    }
}

// 생성한 Target을 into() 메서드의 인자로 넣어줍니다.
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .asBitmap()        // 리소스를 Btimap으로 강제하기 위해
     .into(target)
~~~

## RequestListener
* Glide에서 Callback 리스너를 제공합니다. 리스너는 다음의 2가지 콜백을 받습니다.
    * onException : 이미지 로드 중, 예외가 생겼을 때
    * onResourceReady : 이미지 로드가 완료됬을 때
* 각 콜백 메서드는 boolean 타입 반환인자를 가지고 있습니다. true일 경우, 각 콜백을 핸들링 했다는 의미이므로, Glide가 기본 후처리를 하지 않습니다. 반면에, false일 경우, Glide가 기본 후처리를 진행합니다.
~~~kotlin
private RequestListener<String, GlideDrawable> requestListener = new RequestListener<String, GlideDrawable>() {
    override fun onException(e: Exception, model: String, target: Target<GlideDrawable>, isFirstResource: Boolean): Boolean {
        // 예외사항 처리
        return false;
    }
    
    override fun onResourceReady(resouorce: GlideDrawable, model: String, target: Target<GlideDrawable>, isFromMemoryCache: Boolean, isFirstResource: Boolean): Boolean {
        // 이미지 로드 완료됬을 때 처리
        return false
    }
}

// 생성한 Listener를 Glide 이미지 로드시에 추가해줍니다.
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .listener(requestListener)        //리스너 추가
     .into(target)
~~~

## 애니메이션
* Glide는 이미지 전환시에 애니메이션을 지정할 수 있습니다.
* 기본으로 제공하는 애니메이션으로는 crossFade 애니메이션이 있습니다.
* 그 외에, animate 메서드를 이용해 커스텀 애니메이션을 지정할 수 있습니다.
### ml로 애니메이션 주기
* xml로 원하는 애니메이션을 정의합니다.
* 정의한 애니메이션 리소스를 animate 메서드의 인자로 넘겨줍니다.
~~~xml
// 애니메이션 리소스 준비 (anim.xml 이라 가정)
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate android:fromXDelta="-5%p" android:toXDelta="0" android duration="500" />
    <alpha android:fromAlpha="0.0" android:toAlpha="1.0" android:duration="500" />
</set>
~~~
~~~kotlin
// 생성한 애니메이션 리소스를 이미지 로드시에 추가합니다.
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .animate(R.anim.anim)        // 리소스를 Btimap으로 강제하기 위해
     .into(target)
~~~

### 커스텀 클래스를 이용한 애니메이션
* ViewPropertyAnimation.Animator인터페이스를 구현한 커스텀 클래스를 만듭니다.
* 만든 클래스의 인스턴스를 animate 메서드의 인자로 넘겨줍니다.
~~~kotlin
// 커스텀 애니메이션 클래스 준비
val animationObject = ViewPropertyAnimation.Animator() {  
    override fun animate(view: View) {
        // if it's a custom view class, cast it here
        // then find subviews and do the animations
        // here, we just use the entire view for the fade animation
        view.setAlpha( 0f )

        var fadeAnim = ObjectAnimator.ofFloat( view, "alpha", 0f, 1f )
        fadeAnim.setDuration( 2500 )
        fadeAnim.start()
    }
};

// 커스텀 클래스의 인스턴스를 이미지 로드시에 추가합니다.
Glide.with(context)
     .load("http://www.example.com/icon.png")
     .animate(animationObject)        // 리소스를 Btimap으로 강제하기 위해
     .into(target)
~~~