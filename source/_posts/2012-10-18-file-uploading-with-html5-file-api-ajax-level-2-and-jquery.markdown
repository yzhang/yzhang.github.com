---
layout: post
title: "File uploading with html5 file api and XMLHttpRequest level 2"
date: 2012-10-18 09:20
comments: true
categories: 
icon: a
---

With the new HTML5 file API and XMLHttpRequest level 2, now it's super easy to upload file with AJAX and progressing indicator, finally, it's time to say goodbye to flash uploaders.

<!-- more -->

First, define a file input and a progress indicator.

``` html
<input type=file class="file"></input>
<span class="progress"></span>
```

Then handle uploading with file input's change event, as jQuery didn't support progress event yet, we need access the raw XHR object to listen the progress event.

``` coffeescript
$ ->
  $(".file").change ->
    progress   = $('span.progress')
    file_input = $('input.file')
    file       = file_input[0].files[0]

    if file && pic.size > 1024000
      file_input.val('')
      return alert("File size can't exceed 1M")
    
    data  = new FormData()
    data.append("file", file) if file
    
    if file
      progress.text("1%")
      $.ajax
        type: 'POST'
        url:  '/upload'
        data: data
        cache: false
        contentType: false
        processData: false
        xhr: ->
          myXhr = $.ajaxSettings.xhr()
          if myXhr.upload
            myXhr.upload.addEventListener 'progress', (e) ->
              percentage = Math.floor(e.loaded * 100 / e.total)  
              if percentage == 100
                progress.text("processing...")
              else
                progress.text("#{percentage}%")
            , false
          return myXhr
        success: (data) ->
          progress.text("done.")
```

Super easy, right?