## Camera

- 사진 저장 권한을 위해 아래의 코드를 Manifest.xml에 추가해야 함
```java
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

<!-- 사진을 저장하기 위한 파일에 대한 권한을 획득하기 위한 설정 -->
  <provider
      android:name="android.support.v4.content.FileProvider"
      android:authorities="${applicationId}.provider"
      android:exported="false"
      android:grantUriPermissions="true">
<!-- resource 파일을 res/xml 폴더안에 생성 -->
  <meta-data
      android:name="android.support.FILE_PROVIDER_PATHS"
      android:resource="@xml/file_path"/>
 </provider>
```
- res > xml폴더 추가 후 file_path 파일 생성 하여 내용 추가
```java
<!-- path = /External Storage/CameraN 가 된다 -->
<!-- name = content:// 로 시작하는 uri 주소체계의 suffix 가된다 -->
    <external-path name="Camera" path="CameraN" />
```

- 롤리팝 버전까지는 권한 획득없이 사용이 가능, 마시멜로 이상 버전은 파일 프로바이더를 통해 권한을 획득
```java
private void takePhoto(){
    Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
    if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP){
        File photoFile = null;
        try {
            photoFile = createFile();
            if(photoFile != null){
                // 마시멜로 이상 버전은 파일 프로바이더를 통해 권한을 획득
                if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.M){
                    fileUri = FileProvider.getUriForFile(getBaseContext(), BuildConfig.APPLICATION_ID+".provider", photoFile);
                    // 롤리팝 버전은 권한 없이 획득
                } else {
                    fileUri = Uri.fromFile(photoFile);
                }
                intent.putExtra(MediaStore.EXTRA_OUTPUT, fileUri);
                startActivityForResult(intent, Const.Camera.REQ_CAMERA);
            }
        }catch(Exception e){
            Toast.makeText(getBaseContext(), "사진파일 저장을 위한 임시파일을 생성할 수 없습니다.", Toast.LENGTH_SHORT).show();
            return;
        }

    }else { // 롤리팝 미만 버전에서만 바로 실행
        startActivityForResult(intent, Const.Camera.REQ_CAMERA);
    }
}
```

