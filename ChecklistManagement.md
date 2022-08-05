# Checklist Management


![image](https://user-images.githubusercontent.com/77357735/183103255-08c8187a-808e-47b0-b19b-18babdb1ff14.png)


<!-- TABLE OF CONTENTS -->

## Table of Contents

- [Project Description](#project-description)
- [Technologies and Programing Languages](#technologies-and-programming-languages)
- Frontend Design
  - [Frontend Design HTML](#frontend-design-html)
  - [Frontend Design CSS](#frontend-design-css)
  - [Frontend Design JavaScript](#frontend-design-javascript)
- Backend
  - [Backend Handling with PHP](#backend-handling-with-php)
- [Contact](#contact)

<!-- HOW TO RUN -->
## Project Description

## Technologies and Programming Languages



## Frontend Design HTML

```html
<div id="overDiv">
        <div id="formPanel">
            <ul class="dynamicButtonList">
                <?php while ($res = mysql_fetch_row($result)) { ?>
                    <li class='list_item'>
                        <span class="input-text-1-wtp pdfPreview button-gray-1-wtp button-1-wtp"
                              data-url="<?php echo "/eins/html/" . $res[2]; ?>">
                            <span class="input-text-1-wtp pdfPreviewText">
                                <?php echo utf8_decode($res[1]); ?>
                            </span>
                        </span>
                        <div id="delete_<?php echo $res[0]; ?>" class="deletePdf button-1-wtp">
                            <span class='ui-icon ui-icon-trash deleteIcon '></span>
                        </div>
                    </li>
                <?php } ?>
            </ul>
        </div>
        <div id="buttonPanel">
            <div class="button-1-wtp" id="newChecklist">
                Checkliste hinzufügen
            </div>
            <div id="displayDocuWrap">
                <div id="displayDocu" class="button-gray-1-wtp button-1-wtp">
                    Dokumentation anzeigen
                </div>
            </div>
        </div>
    </div>
```

## Frontend Design CSS

````css
<style type="text/css">
            #overDiv {
                /*position: absolute;*/
                height: 340px;
                width: 100%;
                margin: 0;
                padding: 0;
                top: 0;
                left: 0;
            }

            #formPanel {
                position: relative;
                float: left;
                width: 70%;
                height: 98%;
                margin: 0;
                padding: 0;
                overflow-y: scroll;
            }

            .dynamicButtonList {
                margin-top: 1%;
                margin-bottom: 1%;
            }

            .list_item {
                position: relative;
                height: 24px;
                width: 90%;
                font-size: 12px;
                list-style-type: none;
                margin-bottom: 10px;
                text-align: left;
            }

            #buttonPanel {
                position: relative;
                float: right;
                height: 98%;
                width: 30%;
                margin: 0;
                padding: 0;
                text-align: center;
            }

            #newChecklist {
                position: relative;
                width: 80%;
                margin: 0 auto;
                float: unset;
            }

            #displayDocuWrap {
                position: absolute;
                width: 100%;
                bottom: 0;

            }

            #displayDocu {
                position: relative;
                width: 80%;
                margin: 0 auto;
                float: unset;
            }

            body {
                height: auto;
                overflow-x: hidden;
                overflow-y: hidden;
                padding-bottom: 0;
                margin-bottom: 0;
            }

            .pdfPreview {
                position: relative;
                width: 85%;
                height: 100%;
                white-space: nowrap;
                overflow: hidden;
                text-overflow: ellipsis;
            }

            .pdfPreviewText {
                position: relative;
                width: 100%;
                overflow: hidden;
                display: block;
                text-overflow: ellipsis;
                color: white;
            }

            .deletePdf {
                height: 28px;
                width: 10%;
                float: right;
            }

            .deleteIcon {
                margin-right: auto;
                margin-left: auto;
            }

            .belowCenter {
                position: relative;
                display: block;
                float: unset;
                margin-left: auto;
                margin-right: auto;
                margin-bottom: 0;
                margin-top: 0;
                /*width: 650px;*/
                padding: 0;
                height: 26px;
                text-align: center;
            }

            .divider {
                position: relative;
                border-bottom: 1px solid #F08A00;
                height: 0;
                margin-bottom: 3px;
                padding-bottom: 10px;
                margin-left: 1%;
                margin-right: 1%;
            }

            #jumpToOwnText {
                color: white;
            }

            .container-file-upload-1-wtp {
                width: 300px;
                margin-bottom: 10px;

            }

            .dynamicButtonList > li > :first-child > .ui-button-text {
                padding: 0;
            }

            .container-file-upload-1-wtp {
                height: 20px;
                width: 280px;
            }
            
            #fakeButton {
                background: rgb(255,156,56);
                color: white;
                border: 0;
                position: relative;
                height: 100%;
                margin: -4px;
            }

            #overButton {
                width: 280px;
                height: 25px;
                margin-bottom: 10px;
                margin-top: 5px;
                margin-left: 12px;
            }

            #submitButton {
                margin-top: 5px;
            }

            #filename {
                width: 184px;
                position: relative;
                height: 100%;
                background-color: rgb(102,96,92);
                border: 0;
                color: white;
            }

        </style>
````

## Frontend Design JavaScript
````javascript
<script>

        $('.deletePdf')
            .addClass("ui-state-hover")
            .mouseout(function () {
                $(this)
                    .addClass("ui-state-hover");
            });

        function HandleBrowseClick() {
            var fileinput = document.getElementById("upload");
            fileinput.click();
        }

        function Handlechange() {
            var fileinput = document.getElementById("upload");
            var textinput = document.getElementById("filename");
            textinput.value = fileinput.value.replace(/.*fakepath[\/\\]/i, '');
        }

        $('.container-file-upload-1-wtp').button();

        $(".input-file-upload-button-1-wtp").change(function (e) {
            $("#uploadFile").val($(this).val());
        });
        $(".container-file-upload-1-wtp").mouseover(function (e) {
            $(this).css("cursor", "pointer");
        }).click(function (e) {
//            $("#upload").trigger("click");
        });
        $("#uploadFile").mouseover(function (e) {
            $(this).css("cursor", "pointer");
        });

        $(document).ready(function () {
            $("#jumpToOwn").on("change", function (e) {
                e.preventDefault();
                var check = document.getElementById("jumpToOwn").checked ? 1 : 0;
                $.ajax({
                    type: "GET",
                    url: "admin_interface.php?checkbox=" + check,
                    success: function (e) {
                        console.log(e);
                        var msg = {
                            type: "notify",
                            style: "default",
                            title: "Wert gespeichert",
                            text: "",
                            expires: 2000
                        };
                        parent.postMessage(msg, "http://<?php echo $_SERVER["HTTP_HOST"];?>");
                    },
                    error: function (e) {
                        alert("Fehler beim Speichern des Wertes, bitte versuchen Sie es erneut oder kontaktieren Sie Ihren Suppor");
                        $("#jumpToOwn").attr("checked");
                        console.log(e.data);
                    }
                });
            });
            $("#dontFlatten").on("change", function (e) {
                e.preventDefault();
                var check = document.getElementById("dontFlatten").checked ? 1 : 0;
                $.ajax({
                    type: "GET",
                    url: "admin_interface.php?dontFlatten=" + check,
                    success: function (e) {
                        var msg = {
                            type: "notify",
                            style: "default",
                            title: "Wert gespeichert",
                            text: "",
                            expires: 2000
                        };
                        parent.postMessage(msg, "http://<?php echo $_SERVER["HTTP_HOST"];?>");
                    },
                    error: function (e) {
                        alert("Fehler beim Speichern des Wertes, bitte versuchen Sie es erneut oder kontaktieren Sie Ihren Suppor");
                        $("#dontFlatten").attr("checked");
                    }
                });
            });
            document.addEventListener("DOMNodeInserted", function (e) {
                $(".deletePdf").off().button().click(function (e) {
                    e.preventDefault();
                    var id = this.id.substring(7);
                    var tmp = $(this);
                    var choice = window.confirm("Dieses Dokument wird unwiderruflich gelöscht. Fortfahren?");
                    if (choice == true) {
                        $.ajax({
                            url: "admin_interface.php?delete=" + id,
                            method: "GET",
                            success: function (e) {
                                //parent.postMessage("close_modal", "http://<?php echo $_SERVER["HTTP_HOST"];?>");
                                tmp.parent().remove();
                            },
                            error: function (e) {
                                alert("Es ist ein Fehler aufgetreten, bitte noch einmal probieren oder Ihren Support kontaktieren.");
                            }
                        })
                    }
                });
                $(".pdfPreview").off().button().click(function (event) {
                    event.preventDefault();
                    window.open($(this).data("url"), "_blank");
                });
            });

            $("#uploadForm").submit(function (e) {
                e.preventDefault();
                var formData = new FormData($('form')[2]);
                $.ajax({
                    url: "admin_interface.php?upload=1",
                    type: "POST",
                    data: formData,
                    processData: false,
                    contentType: false,
                    success: function (e) {
                        $.modal.close();
                        $.get("admin_interface.php?getLast=1", function (data) {
                            $(".dynamicButtonList").append(data);
                        });
                    },
                    error: function (e) {
                        alert("Fehler beim Hochladen");
                        console.log(e.data);
                    }
                });
            });
            $(".pdfPreview").off().button().click(function (event) {
                event.preventDefault();
                window.open($(this).data("url"), "_blank");
            });
            $("#newChecklist").button().click(function (event) {
                event.preventDefault();
                $.modal($("#uploadDiv"));
//                {
//                    onOpen: function (dialog) {
//                        dialog.overlay.css("width", "100%");
//                        dialog.overlay.css("height", "100%");
//                        dialog.overlay.fadeIn("fast", function () {
//                            dialog.container.slideDown("fast", function () {
//                                dialog.data.fadeIn("fast");
//                            });
//                        });
//                    }
//                }
            });
            $("#displayDocu").button().click(function (event) {
                event.preventDefault();
                window.open("/eins/html/downloads/formulare/checklisten/documentation/doku.pdf", "Doku", "height=600,width=800");
            });
            $("body").css("height", document.body.scrollHeight);
            $(".deletePdf").off().button().click(function (e) {
                e.preventDefault();
                var id = this.id.substring(7);
                var tmp = $(this);
                var choice = window.confirm("Dieses Dokument wird unwiderruflich gelöscht. Fortfahren?");
                if (choice == true) {
                    $.ajax({
                        url: "admin_interface.php?delete=" + id,
                        method: "GET",
                        success: function (e) {
                            //parent.postMessage("close_modal", "http://<?php echo $_SERVER["HTTP_HOST"];?>");
                            tmp.parent().remove();
                        },
                        error: function (e) {
                            alert("Es ist ein Fehler aufgetreten, bitte noch einmal probieren oder Ihren Support kontaktieren.");
                        }
                    })
                }
            });
        });
    </script>
````
## Backend Handling with PHP

````php
if (isset($_GET["delete"])) {
    $sql = "SELECT checkl_url,checkl_name FROM can_checklisten WHERE id = " . $_GET["delete"];
    $checkl = mysql_query($sql);
    list($checkl_url, $checkl_name) = mysql_fetch_row($checkl);
    $sql = "DELETE FROM can_checklisten WHERE id = " . $_GET["delete"];
    $result = mysql_query($sql, $dbi);
    if ($result === true && mysql_affected_rows() > 0) {
        $fN = basename($checkl_url);
        $deleteRelativeFolder = "downloads/formulare/checklisten/own/";
        $tD = realpath(dirname(__FILE__) . "/../../../" . $deleteRelativeFolder);
        $delFile = $tD . "/" . $fN;

        unlink($delFile);
        exit();
    } else {
        header("HTTP/1.0 404 Not Found");
        http_response_code(404);
        die();
    }
} else if ($_GET["upload"] == 1) {
    while (true) {
        $uploadRelativeFolder = "downloads/formulare/checklisten/own/";
        $tD = realpath(dirname(__FILE__) . "/../../../" . $uploadRelativeFolder);
        $length = 30;
        $characters = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
        $charactersLength = strlen($characters);
        $randomString = '';
        for ($i = 0; $i < $length; $i++) {
            $randomString .= $characters[rand(0, $charactersLength - 1)];
        }
        $rand = $randomString;
        $filename = $rand . ".pdf";
        $tF = $tD . "/" . $rand . ".pdf";
        if (!file_exists($tD)) {
            break;
        }
        break;
    }


    try {

        // Undefined | Multiple Files | $_FILES Corruption Attack
        // If this request falls under any of them, treat it invalid.
        if (
            !isset($_FILES['upfile']['error']) ||
            is_array($_FILES['upfile']['error'])
        ) {
            var_dump($_FILES);
            throw new RuntimeException('Invalid parameters.');
        }

        // Check $_FILES['upfile']['error'] value.
        switch ($_FILES['upfile']['error']) {
            case UPLOAD_ERR_OK:
                break;
            case UPLOAD_ERR_NO_FILE:
                throw new RuntimeException('No file sent.');
            case UPLOAD_ERR_INI_SIZE:
            case UPLOAD_ERR_FORM_SIZE:
                throw new RuntimeException('Exceeded filesize limit.');
            default:
                throw new RuntimeException('Unknown errors.');
        }

        // You should also check filesize here.
        if ($_FILES['upfile']['size'] > 10000000) {
            throw new RuntimeException('Exceeded filesize limit.');
        }

        // DO NOT TRUST $_FILES['upfile']['mime'] VALUE !!
        // Check MIME Type by yourself.
//        $finfo = new finfo(FILEINFO_MIME_TYPE);
//        if (false === $ext = array_search(
//                $finfo->file($_FILES['upfile']['tmp_name']),
//                array(
//                    'pdf' => 'application/pdf',
//                ),
//                true
//            )) {
//            throw new RuntimeException('Invalid file format.');
//        }

        // You should name it uniquely.
        // DO NOT USE $_FILES['upfile']['name'] WITHOUT ANY VALIDATION !!
        // On this example, obtain safe unique string from above.
        if (!move_uploaded_file(
            $_FILES['upfile']['tmp_name'],
            $tF
        )
        ) {
            throw new RuntimeException('Failed to move uploaded file.');
        }

        $sql = "INSERT INTO can_checklisten (checkl_name,checkl_url,checkl_marke) VALUES ('" .
            mysql_real_escape_string($_POST["pdfName"]) . "', '" . $uploadRelativeFolder . $filename . "', 'OWN')";
        $result = mysql_query($sql, $dbi);
        if (mysql_affected_rows() == 0) {
            throw new RuntimeException('DB insert failed');
        }
    } catch (RuntimeException $e) {

        echo $e->getMessage();
        header("HTTP/1.0 404 Not Found");
//        http_response_code(404);
        die();

    }
    echo $tF;
    exit();
} else if ($_GET["getLast"] == 1) {
    $sql = "SELECT id, checkl_name,checkl_url FROM can_checklisten ORDER BY id DESC LIMIT 1";
    $query = mysql_query($sql, $dbi);
    list($id, $name, $url) = mysql_fetch_row($query);
    $out = "<li class='list_item'>" .
        "<span class='pdfPreview input-text-1-wtp button-gray-1-wtp button-1-wtp' data-url='/eins/html/" . $url . "'>" .
        "<span class='pdfPreviewText input-text-1-wtp'>" . $name . "</span></span>" .
        "<div id='delete_" . $id . "' class='deletePdf button-1-wtp'>" .
        "<span class='ui-icon ui-icon-trash deleteIcon '></span>" .
        "</div>" .
        "</li>";
    echo $out;
} else if (isset($_GET["checkbox"]) && ($_GET["checkbox"] == 0 || $_GET["checkbox"] == 1)) {
    $sql = "SELECT * FROM can_wiedenn WHERE wie_module = 'Eigene Checklisten wenn keine markenspezifischen' AND wie_hdl = '$autohauskonto_nr' LIMIT 1";
    $result = mysql_query($sql, $dbi);
    if (mysql_num_rows($result) < 1) {
        $sql = "INSERT INTO can_wiedenn (wie_module, wie_onoff, wie_hdl, wie_firma, wie_filiale) " .
            "VALUES ('Eigene Checklisten wenn keine markenspezifischen', '" . $_GET["checkbox"] . "', $autohauskonto_nr, 999, 999)";
        $res = mysql_query($sql, $dbi) or die(mysql_error());
        echo "insert";
    } else {
        $sql = "UPDATE can_wiedenn SET wie_onoff = " . $_GET["checkbox"] . " WHERE wie_module ='Eigene Checklisten wenn keine markenspezifischen'";
        $res = mysql_query($sql, $dbi) or die(mysql_error());
        echo "changed";
    }
} else if (isset($_GET["dontFlatten"]) && ($_GET["dontFlatten"] == 0 || $_GET["dontFlatten"] == 1)) {
    $sql = "SELECT * FROM can_wiedenn WHERE wie_module = 'Formularfelder auf Checklisten bearbeitbar lassen' AND wie_hdl = '$autohauskonto_nr' LIMIT 1";
    $result = mysql_query($sql, $dbi);
    if (mysql_num_rows($result) < 1) {
        $sql = "INSERT INTO can_wiedenn (wie_module, wie_onoff, wie_hdl, wie_firma, wie_filiale) " .
            "VALUES ('Formularfelder auf Checklisten bearbeitbar lassen', '" . $_GET["dontFlatten"] . "', $autohauskonto_nr, 999, 999)";
        $res = mysql_query($sql, $dbi) or die(mysql_error());
        echo "insert";
    } else {
        $sql = "UPDATE can_wiedenn SET wie_onoff = " . $_GET["dontFlatten"] . " WHERE wie_module ='Formularfelder auf Checklisten bearbeitbar lassen'";
        $res = mysql_query($sql, $dbi) or die(mysql_error());
        echo "changed";
    }
}
````
<!-- CONTACT -->

## Contact

Rahman Habib - [habib2030@web.de](mailto:habib2030@web.de)

LinkedIn Profile: [smhabibjr](https://www.linkedin.com/in/smhabibjr)

Youtube Channel: [HabibJr](https://www.youtube.com/c/HabibJr)

Youtube Channel: [HabibJr](https://www.facebook.com/smhabibjr)
