Yii2 Easy File Upload Behavior
==============================
Yii2 Easy File Upload Behavior

Installation
------------

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist baha2odeh/yii2-easy-fileupload-behavior "*"
```

or add

```
"baha2odeh/yii2-easy-fileupload-behavior": "*"
```

to the require section of your `composer.json` file.


you have to not use UploadedFile::getInstance anymore this can be done by this behavior 

Usage
-----

Once the extension is installed, simply use it in your code by  :
add this on your php model
```php
    /**
     * {@inheritdoc}
     */
    public function rules()
    {
        return [
            [['images_files','image_file'],'file'], //@TODO add your rules here
        ];
    }

    public function behaviors()
    {
       return [
           [
               'class' => EasyFileUploadBehavior::className(),
               'relations' => [
                   'images_files' => 'images', // images means your HasMany Relation Name
                   'image_file' => 'image', // image means your HasOne Relation Name
               ],
               'uploadCallBack' => function($relationName,UploadedFile $file){
               	   // do your magic here and return one model that you save the image on it
               	   // if return is null file will skipped
               	   $file->saveAs('upload-path/'.$file->name);
                   $image = new Image();
                   $image->filename = $file->name;
                   $image->save(false);
                   return $image;
               }
           ]
       ];
    }


    //// demo only ////

    /**
     * @return \yii\db\ActiveQuery
     */
    public function getImage()
    {
        return $this->hasOne(Image::className(), ['image_id' => 'image_id']);
    }

    /**
     * @return \yii\db\ActiveQuery
     */
    public function getArticleImages()
    {
        return $this->hasMany(ArticleImage::className(), ['article_id' => 'id']);
    }

    /**
     * @return \yii\db\ActiveQuery
     */
    public function getImages()
    {
        return $this->hasMany(Image::className(), ['image_id' => 'image_id'])->via('articleImages');
    }
    //// demo only ////

```

then use it on your form like this
```PHP
 <?php $form = ActiveForm::begin(); ?>
    <div class="box-body table-responsive">

        <?= $form->field($model, 'image_file')->fileInput() ?>

        <?= $form->field($model, 'images_files[]')->fileInput(['multiple'=>true]) ?>



    </div>
    <div class="box-footer">
        <?= Html::submitButton(Yii::t('app', 'Save'), ['class' => 'btn btn-success btn-flat']) ?>
    </div>
    <?php ActiveForm::end(); ?>
```










