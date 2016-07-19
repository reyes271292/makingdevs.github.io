---
layout: post
title: "Uso de camara en android"
date: 2016-07-11 22:53:27 -0500
comments: true
author: Juan Francisco Reyes Silva
published: true
---   

Android esta enfoca al desarrollo móvil, como es de esperarse cuenta con las herramientas necesarias para hacer uso del hardware, en esta ocasión se mostrará cómo usar la cámara.   

<!-- more -->

Para poder hacer uso de la cámara se realiza mediante intent, que son el mecanismo por el cual se comunica la aplicación en tiempo de ejecución con otros componentes, así como lanzar eventos, se cuenta con dos tipos los cuales son:    
*Intento implícito     
Se puede iniciar una actividad en otra aplicación en el dispositivo   
*Intento explicito     
Se especifica la clase de la actividad a empezar para que el sistema operativo la inicie

Android cuenta con la clase **MediaStore**, esta se encarga de proveer los medios de comunicación, el que nos interesa es **ACTION_IMAGE_CAPTURE**, este en el intent con el cual podemos hacer uso de la cámara.  

``` groovy   
	void dispatchTakePictureIntent(Context context) {
		Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE)
		if (takePictureIntent.resolveActivity(context.getPackageManager()) != null) {
			try {
				photoFile = mCamaraUtil.createImageFile("IMG")
			} catch (IOException ex) {
				Toast.makeText(getActivity(), R.string.error_take_photo, Toast.LENGTH_SHORT).show()
			}
			if (photoFile != null) {
				takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, Uri.fromFile(photoFile))
				Log.d(TAG,takePictureIntent.getProperties().toString())
				startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO)
			}
		}
	}
```   

* Almacenamiento externo   
Al capturar una foto, esta debe ser almacenada para poder ser usada posteriormente, Android provee una unidad principal para ello, la cual puede ser su almacenamiento interno o una memoria SD.   

Para acceder a ese directorio, Android cuenta con una clase llamada **Environment**, el método que regresa el directorio de almacenamiento común/externo es **getExternalStoragePublicDirectory(…)**, el tipo de archivo son imágenes por lo cual el parámetro para este caso será **Environment.DIRECTORY_PICTURES**.   

``` groovy   
	File createImageFile(String name){
		String timeStamp = new Date().format("yyyyMMdd_HHmmss")
		String imageFileName = "${name}_$timeStamp"
		File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES)
		File image = File.createTempFile(imageFileName,".jpg", storageDir)
	}
```

* Manipular el resultado de la cámara   
Cuando se captura la foto, se deberá manipular su resolución para obtener el archivo que se necesita, como se crea un intent hacia la cámara una vez que termina se maneja el resultado con el método onActivityResult(…).

``` groovy 
	@Override
  void onActivityResult(int requestCode, int resultCode, Intent data) {
	  if (requestCode == REQUEST_TAKE_PHOTO && resultCode == Activity.RESULT_OK) {

		  Bitmap bitmapResize = mCamaraUtil.resizeBitmapFromFilePath(photoFile.getPath(),1280,960)
		  File photo = mCamaraUtil.saveBitmapToFile(bitmapResize,photoFile.getName())
		  mImageUtil.addPictureToGallery(getActivity(),photo.getPath())
		  Toast.makeText(getActivity(), R.string.success_take_photo, Toast.LENGTH_SHORT).show()

		} else {
			Toast.makeText(getActivity(), R.string.error_take_photo, Toast.LENGTH_SHORT).show()
		}
  }
``` 

Como se mencionó se debe ajustar la resolución de la foto capturada, como se muestra a continuación:   

``` groovy
Bitmap resizeBitmapFromFilePath(String pathPhoto, Integer width, Integer height){
	BitmapFactory.Options bmOptions = new BitmapFactory.Options()
	Bitmap bitmap = BitmapFactory.decodeFile(pathPhoto,bmOptions)
	bitmap = Bitmap.createScaledBitmap(bitmap,width,height,true)
}
```

Una vez culminado el ajuste de la foto, se procede a guardar el archivo con los cambios.   

``` groovy

    File saveBitmapToFile(Bitmap bitmap,String photoName){
        ByteArrayOutputStream bytes = new ByteArrayOutputStream()
        bitmap.compress(Bitmap.CompressFormat.PNG, 100, bytes)
        File file = new File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES).toString() + File.separator + photoName)
        FileOutputStream fileOutputStream
        try {
            file.createNewFile()
            fileOutputStream = new FileOutputStream(file)
            fileOutputStream.write(bytes.toByteArray())
            fileOutputStream.close()
        }catch (Exception e){
            Log.d(TAG,"Error... "+e.message)
        }
        file
    }
```

Cuando el proceso se haya terminado, se anexa la foto a la galería para que el usuario pueda visualizar sin problemas su archivo, como se muestra.  

``` groovy 
	public static void addPictureToGallery(Context context, String picturePath) {
		Intent mediaScanIntent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE)
		File f = new File(picturePath)
		Uri contentUri = Uri.fromFile(f)
		mediaScanIntent.setData(contentUri)
		context.sendBroadcast(mediaScanIntent)
  }
```  

* Permisos de Android      
A la hora de manejar el hardware se debe de pedir ciertos permisos como son el escribir y leer en la memoria externa, así como usar la cámara, para esto se usa la etiqueta ``<uses-permission>`` donde se coloca que permiso es solicitado.  
``<uses-feature android:name="android.hardware.camera" android:required="true" />``
``<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>``   

Pueden encontrar el código completo [*aquí*][1].

[1]: https://github.com/reyes271292/camera_android

[2]: https://developer.android.com/reference/android/provider/MediaStore.html

[3]: https://developer.android.com/reference/android/content/Intent.html

[4]: https://developer.android.com/reference/android/os/Environment.html

[5]: https://developer.android.com/training/basics/intents/result.html





