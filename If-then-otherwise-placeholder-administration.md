# If-then-otherwise Placeholder Administration (Extend ckeditor-4 Toolbar)


![image](https://user-images.githubusercontent.com/77357735/209977285-454039bf-64cb-4570-bfb9-7e23f558d978.png)


<!-- TABLE OF CONTENTS -->

## Table of Contents

- [Add new toolbar icon in ck-editor](#add-new-toolbar-icon-in-ckeditor)
- [Add modal box when click the new added icon](#open-modal-box)
- [Backend Handling with Ruby](#backend-handling-with-ruby)
- [Contact](#contact)

## Add new toolbar icon in ckeditor

![image](https://user-images.githubusercontent.com/77357735/209978014-e10e2f51-246b-47a7-9a2d-c68a96ae90b1.png)

create a new plugin file under CKEditor/plugins folder.
````
R:\odbcrunner\app\assets\javascripts\ckeditor\plugins\placeholderSetting\plugin.js

CKEDITOR.plugins.add('placeholderSetting',
    {
        init: function (editor) {
            // add placeholder menu with icon to the editor
            editor.ui.addButton('placeholderSetting',
                {
                    label: "Platzhalter hinzufügen",
                    command: 'mySimpleCommand',
                    toolbar: 'insert,100',
                    icon: this.path + 'setting-48.png',
                });
        }
    });
````

Initialize new added plugin in config.js file as extraPlugins.

````javaScript
CKEDITOR.editorConfig = function (config) {
    // Define changes to default configuration here.
    // For complete reference see:
    // http://docs.ckeditor.com/#!/api/CKEDITOR.config
    config.extraPlugins = 'entities';

    config.uiColor = '#ff9c38';

    config.extraPlugins = 'strinsert,base64image,attach,placeholderSetting';
    // // The toolbar groups arrangement, optimized for two toolbar rows.
    config.toolbar = [
        { name: 'clipboard', groups: [ 'clipboard', 'undo' ], items: [ 'Cut', 'Copy', 'Paste', 'PasteText', '-', 'Undo', 'Redo' ] },
        { name: 'links', items: [ 'Link', 'Unlink', 'Anchor' ] },

        { name: 'insert', items: [ 'base64image', 'Table', 'HorizontalRule', 'SpecialChar' ] },
        { name: 'tools', items: [ 'Maximize', 'Attachments'] },
        { name: 'others', items: [ 'strinsert', 'placeholderSetting' ] },
        '/',
        { name: 'basicstyles', groups: [ 'basicstyles', 'cleanup' ], items: [ 'Bold', 'Italic', 'Strike', 'RemoveFormat' ] },
        { name: 'paragraph', groups: [ 'list', 'indent', 'blocks', 'align', 'bidi' ], items: [ 'NumberedList', 'BulletedList', '-', 'Outdent', 'Indent', '-', 'Blockquote' ] },
        { name: 'styles', items: [ 'Format' ] }
    ];

    // Set the most common block elements.
    config.format_tags = 'h1;h2;h3;pre';

    // Simplify the dialog windows.
    config.removeDialogTabs = 'image:advanced;link:advanced';

    config.enterMode = CKEDITOR.ENTER_BR;

    // Do not load any file. The styles set is empty.
    config.stylesSet = false;
};
````
## Open Modal Box

Custom placeholder modal box (using HAML)

````hmal
#modal_for_placeholder_setting{:style => "display: none; width:500px; height: 400px;"}
  .main-container
    .header-section{:style => "display: flex; justify-content:center;margin-bottom:15px"}
      %label{:style => "font-size:25px;font-weight:bold"} Platzhaltereinstellung
      = hidden_field_tag :get_hdl_id,  params[:hdl_id]
      = hidden_field_tag :get_hdl_filiale, @branch[:hdl_filiale]
      = hidden_field_tag :get_hdl_firma, @branch[:hdl_firma]
    %hr{:style => "border: solid 1px #F08A00"}
    .add-placeholder
      %label{ for: 'text-vorgaben', :style => "font-size:15px;font-weight:bold"} Neuer Platzhalter

      .placeholder_div{:id => "placeholder_div",:style => "display:flex; justify-content: space-between; margin-top:5px;width:100%;"}
        = hidden_field_tag :existing_placeholder_db_id,0,{:class => "placeholder_db_id"}
        = text_field_tag("new_and_existing_placeholder", '',  :style => "width:98%;height:24px;margin-bottom:10px;", :class => "placeholder_name form-inputs", :type => "text",:required => true, :name => "existing_placeholder")
        .delete-button-div{:style => "display:none"}
          .btn.btn-danger.border-0.rounded-0{:id => "placeholder-del-btn", :style => "background:rgb(255, 156, 56);height: 30px;width: 30px;display: flex;justify-content: center;align-items: center;"}
            %i.fa.fa-trash
      .wenn-label
        %label{:style => "font-size:15px;font-weight:bold"} WENN
      .row{:style => "margin-top: 5px; display:flex; justify-content:space-between"}
        .column
          %label{ for: 'placeholder-selector' ,:style => "font-size:15px;"} Eingabe
        .column
          %label{ for: 'text-vorgaben', :style => "font-size:15px"} Vergleich
        .column
          %label{ for: 'text-vorgaben', :style => "font-size:15px"} Vergleichen mit

      .row{:style => "margin-top: 5px; display:flex; justify-content:space-between;width: 98%;"}
        .column{:style => "width:100%;height:30px;"}
          = select_tag :placeholder_name, options_for_select([], params[:placeholder_name]),style: "width:100%;height:100%;",:required => true, class:"form-inputs ", id: "placeholder-selector"
        .column{:style => "width:100%;height:30px;"}
          = select_tag :compaire, options_for_select([]), class: "form-inputs",:required => true, style: "width:100%;height:100%;", id: "placeholder-compare"
        .column{:style => "width:100%;height:30px;"}
          %span{:id => "compare-with-area" }
          -#=text_field_tag("compare-with", '',  :style => "width:100%;height:24px", :class => "compare-with validation", :type => "text",:required => true, :placeholder => "", :name => "compare-with")
    .add-placeholder-result{:style => "margin-top:5px"}
      %label{:style => "font-size:15px;"} Dann diesen Text einfügen:
      .placeholder-result{:style => "display: flex;margin-top:5px"}
        = text_area_tag 'dann-text', nil, class: 'dann-text form-inputs ', style: 'width:100%;height:40px;resize:none;'
    .add-placeholder-result{:style => "margin-top:5px"}
      %label{:style => "font-size:15px;"} Sonst diesen Text einfügen:
      .placeholder-result{:style => "display: flex;margin-top:5px"}
        = text_area_tag 'sonst-text', nil, class: 'sonst-text form-inputs ', style: 'width:100%;height:40px;resize:none;'
    .placeholder-save{:style => "display: flex; justify-content:center;margin-top:15px"}
      .button-1-wtp.buttonWithBoldText-1-wtp.button-padding-1-wtp#savePlaceholder{:type => "submit"} Speichern
````
Add functionality

````javaScript
$(document).ready(function () {
                // after click the placeholder setting icon
                $(".cke_button__placeholdersetting").off().click(function () {
                    $("#existing_placeholder_db_id").val(0);
                    $("#existing_placeholder").css("width", "98%");
                    $(".delete-button-div").css("display", "none");
                    $("#new_and_existing_placeholder").val('');
                    $("#dann-text").val('');
                    $("#sonst-text").val('');
                    $('#placeholder-compare').find('option').remove().end();
                    // open a new the placeholder setting modal box
                    $("#modal_for_placeholder_setting").modal();
                    // grab the existing placeholder and make a new placeholder dropdown menu
                    $("#new_and_existing_placeholder").focus();
                    var placeholder_ = get_keywords_for_communication();
                    remove_some_placeholder = $.grep(placeholder_, function (placeholder, index) {
                        return placeholder[0] !== '__HDL_NAME__' && placeholder[0] !== '__EINGRIFFE_FORTLAUFEND__';
                    });
                    var _placeholder_selector = $('#placeholder-selector');
                    _placeholder_selector.find('option').remove().end().append('<option value="">Auswählen</option>');
                    if (_placeholder_selector.prop('options').length !== remove_some_placeholder.length) {
                        for (var i = 0; i < remove_some_placeholder.length; i++) {
                            var placeholder_value = remove_some_placeholder[i][0];
                            var placeholder_text = remove_some_placeholder[i][1];
                            _placeholder_selector.append($(document.createElement('option')).prop({
                                value: placeholder_value,
                                text: placeholder_text
                            }));
                        }
                    }

                    var selector = $("#compare-with-area");
                    selector.html(getTxtbox());
                    //on change update the compaire value
                    _placeholder_selector.on("change", function () {
                        var selected_option = $(this).val();
                        $.ajax({
                            url: "/communication/templates/update_custom_placeholder",
                            type: "POST",
                            data: {placeholder_value: selected_option},
                            dataType: 'json',
                            success: function (data) {
                                const _response_data = data;
                                if (_response_data.length > 0) {
                                    for (var i = 0; i < _response_data.length; i++) {
                                        var response_object = {
                                            status: _response_data[i].status,
                                            pl_db_id: _response_data[i].pl_db_id,
                                            branch_id: _response_data[i].branch_id,
                                            custom_pl_name: _response_data[i].custom_pl_name,
                                            custom_pl_value: _response_data[i].custom_pl_value,
                                            pl_name: _response_data[i].pl_name,
                                            pl_value: _response_data[i].pl_value,
                                            compare_value: _response_data[i].compare_value,
                                            compare_with: _response_data[i].compare_with,
                                            dann_text: _response_data[i].dann_text,
                                            sonst_text: _response_data[i].sonst_text,
                                        };
                                        if (_response_data[i].status === 'placeholder already exist') {
                                            $("#existing_placeholder").css("width", "90%");
                                            $(".delete-button-div").css("display", "block");
                                            $("#existing_placeholder_db_id").val(response_object['pl_db_id']);
                                            update_or_create_new_placeholder(selected_option, response_object);
                                        } else if (_response_data[i].status === 'placeholder not exist') {
                                            $("#existing_placeholder").css("width", "98%");
                                            $(".delete-button-div").css("display", "none");
                                            $("#existing_placeholder_db_id").val(0);
                                            update_or_create_new_placeholder(selected_option);
                                        }
                                    }
                                }
                            },
                            error: function (data) {
                            }
                        });

                        _compare_operator = [
                            ['gleich', 'Gleich'],
                            ['ungleich', 'Ungleich'],
                            ['kleiner_als', 'Kleiner als'],
                            ['groesser_als', 'Größer als'],
                            ['kleiner_oder_gleich', 'Kleiner oder gleich'],
                            ['groesser_or_gleich', 'Großer oder gleich']
                        ];
                    });

                    function update_or_create_new_placeholder(selected_option, data_object = 0) {
                        var {status, pl_value,custom_pl_value} = data_object;
                        var hdl_firma = $("#get_hdl_firma").val();
                        var hdl_filiale = $("#get_hdl_filiale").val();
                        if (data_object === 0) {
                            var _seleted_placeholder = selected_option;

                        } else {
                            _seleted_placeholder = pl_value;
                        }

                        switch (_seleted_placeholder) {
                            case '__MARKE__':
                                selector.html(getTxtbox());
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(2, "z.B: DAC", data_object);

                                } else {
                                    update_compare_operator_and_placeholder(2, "z.B: DAC");
                                }
                                check_ele_has_date_or_time_picker();
                                break;
                            case '__WP_AUTO__':
                                selector.html(getTxtbox());
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(2, "Dacia Logan Express", data_object);
                                } else {
                                    update_compare_operator_and_placeholder(2, "Dacia Logan Express");
                                }
                                check_ele_has_date_or_time_picker();
                                break;
                            case '__WP_KENNZ__':
                                selector.html(getTxtbox());
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(2, "MEI-XC 2753", data_object);
                                } else {
                                    update_compare_operator_and_placeholder(2, "MEI-XC 2753");
                                }
                                check_ele_has_date_or_time_picker();
                                break;
                            case '__BENUTZER_NAME__':
                                selector.html(getDropdown('benutzer_or_kundenberater'));
                                getUserAndKundenberater("username", hdl_firma, hdl_filiale);
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(2, "1-wtp Support", data_object);
                                } else {
                                    update_compare_operator_and_placeholder(2, "MEI-XC 2753");
                                }
                                check_ele_has_date_or_time_picker();
                                break;
                            case '__WP_DATUM__':
                                selector.html(getTxtbox());
                                $("#compare-with").addClass("date-picker");
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(_compare_operator.length, "12.10.2022", data_object);
                                } else {
                                    update_compare_operator_and_placeholder(_compare_operator.length, "12.10.2022");
                                }
                                check_ele_has_date_or_time_picker();
                                break;
                            case '__WP_ZEIT__':
                                selector.html(getTxtbox());
                                $("#compare-with").addClass("time-picker");
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(_compare_operator.length, "07:15", data_object);
                                } else {
                                    update_compare_operator_and_placeholder(_compare_operator.length, "07:15");
                                }
                                check_ele_has_date_or_time_picker();
                                break;
                            case '__ABHOLDATUM__':
                                selector.html(getTxtbox());
                                $("#compare-with").addClass("date-picker");
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(_compare_operator.length, "12.10.2022", data_object);
                                } else {
                                    update_compare_operator_and_placeholder(_compare_operator.length, "07:15");
                                }
                                check_ele_has_date_or_time_picker();
                                break;
                            case '__ABHOLZEIT__':
                                selector.html(getTxtbox());
                                $("#compare-with").addClass("time-picker");
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(_compare_operator.length, "07:15", data_object);
                                } else {
                                    update_compare_operator_and_placeholder(_compare_operator.length, "07:15");
                                }
                                check_ele_has_date_or_time_picker();
                                break;
                            case '__ERSATZWAGEN__':
                                selector.html(getDropdown('ersatzwagen'));
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(2, "ja / nein", data_object);
                                } else {
                                    update_compare_operator_and_placeholder(2, "ja / nein");
                                }
                                check_ele_has_date_or_time_picker();
                                break;
                            case '__EINGRIFFE_BLOCK__':
                                selector.html(getTxtbox());
                                $("#placeholder-compare").empty();
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(0, "Hauptuntersuchung", data_object);
                                } else {
                                    update_compare_operator_and_placeholder(0, "Hauptuntersuchung");
                                }
                                check_ele_has_date_or_time_picker();
                                break;
                            case '__KUNDENBERATER__':
                                selector.html(getDropdown('benutzer_or_kundenberater'));
                                getUserAndKundenberater("kundenberater", hdl_firma, hdl_filiale);
                                if (status === "placeholder already exist") {
                                    update_compare_operator_and_placeholder(2, "Kalle Annahme", data_object);
                                } else {
                                    update_compare_operator_and_placeholder(2, "Kalle Annahme");
                                }
                                check_ele_has_date_or_time_picker();
                        }
                    }


                    function getUserAndKundenberater(parameter, firma = 0, filiale = 0) {
                        $.ajax({
                            url: "/communication/templates/get_user_and_kundenberater",
                            type: "POST",
                            async: false,
                            data: {placeholder_name: parameter, die_firma: firma, die_filiale: filiale},
                            dataType: 'json',
                            success: function (data) {
                                var _response_date = data;
                                if (_response_date.length > 0) {
                                    for (var i = 0; i < _response_date.length; i++) {
                                        $("#compare-with").append($(document.createElement('option')).prop({
                                            value: _response_date[i],
                                            text: _response_date[i]
                                        }))
                                    }
                                }
                            },
                            error: function (data) {
                            }


                        });
                    }


                    // default input text field
                    function getTxtbox() {
                        return '<input class="compare-with form-inputs" type="text" id="compare-with" style="width:100%;height:80%;" required>'
                    }

                    // dropdown menu for ersatzwagen , benutzer and kundenberater
                    function getDropdown(placeholder) {
                        if (placeholder === "ersatzwagen") {
                            return [
                                '<select name="ersatzwagen_status" id="compare-with" title="Auswählen" style="width:106%;height:30px" class="form-inputs compare-with" data-width="15%" required>',
                                '<option value="ja">Ja</option>',
                                '<option value="nein">Nein</option>',
                                '</select>'
                            ].join('');
                        } else if (placeholder === "benutzer_or_kundenberater") {
                            return [
                                '<select id="compare-with" title="Auswählen" style="width:106%;height:30px" class="form-inputs compare-with" data-width="15%" required>',
                                '<option value="">Auswählen</option>',
                                '</select>'
                            ].join('');
                        }
                    }

                    // check compare with input field has time or date picker
                    function check_ele_has_date_or_time_picker() {
                        if ($("#compare-with").hasClass("date-picker")) {
                            $("#compare-with").attr("readonly", true);
                            run_date_picker();
                        } else if ($("#compare-with").hasClass("time-picker")) {
                            $("#compare-with").attr("readonly", true);
                            run_time_picker();
                        }
                    }

                    // run time picker
                    function run_time_picker() {
                        $(".time-picker").timepicker({
                            rows: 2,
                            showPeriodLabels: false,
                            minuteText: 'Minute',
                            hourText: 'Stunde',
                            closeText: 'Fertig',
                            showCloseButton: true,
                            minutes: {
                                starts: 0,
                                interval: 5
                            },
                            //stunden anzeige
                            hours: {
                                starts: 5,
                                ends: 22
                            }
                        });
                    }

                    // run date picker
                    function run_date_picker() {
                        $(".date-picker").datepicker({
                            addSliderAccess: true, <!-- Formatierung für Touchdevices. Buttons zur Touchbedienung -->
                            sliderAccessArgs: {touchonly: false}, <!-- Touch -->
                            changeYear: true,
                            showButtonPanel: false,
                            dateFormat: 'dd.mm.yy', <!-- Deutsches Format fuer Textfeld -->
                            showOtherMonths: true,
                            numberOfMonths: 2,
                            showWeek: true,
                            showAnim: 'slideDown',
                            altField: '#hidden_range_from',
                            altFormat: "yy-mm-dd",
                            onClose: function (date) {
                            }
                        });
                    }

                    // function to change the compare operators
                    function update_compare_operator_and_placeholder(compare_operator_length, placholder_text, update_data = 0) {

                        $("#placeholder-compare").empty();

                        $("#compare-with").attr("placeholder", placholder_text);
                        if (compare_operator_length === 0) {
                            $("#placeholder-compare").append($(document.createElement('option')).prop({
                                value: "include",
                                text: "Enthalten"
                            }));
                        } else if (compare_operator_length > 0) {
                            for (var i = 0; i < compare_operator_length; i++) {
                                var compare_operator_value = _compare_operator[i][0];
                                var compare_operator_text = _compare_operator[i][1];
                                $("#placeholder-compare").append($(document.createElement('option')).prop({
                                    value: compare_operator_value,
                                    text: compare_operator_text
                                }));
                            }
                        }
                        // update existing placeholder if placeholder exist in db
                        if (update_data !== 0) {
                            var {status, pl_db_id, custom_pl_name, pl_value, compare_value, compare_with, dann_text, sonst_text} = update_data;
                            $("#new_and_existing_placeholder").val(custom_pl_name);
                            $("#placeholder-selector").val(pl_value);
                            $("#placeholder-compare").val(compare_value);
                            $("#compare-with").val(compare_with);
                            $("#dann-text").val(dann_text);
                            $("#sonst-text").val(sonst_text);
                        } else {
                            $("#dann-text").val('');
                            $("#sonst-text").val('');
                        }
                    }


                    $("#savePlaceholder").off().click(function (e) {
                        e.preventDefault();
                        // grab the form value after click the save button and save these as an object
                        var _data = {
                            placeholder_db_id: $("#existing_placeholder_db_id").val(),
                            new_placeholder_name: $("#new_and_existing_placeholder").val(),
                            placeholder_name: $('#placeholder-selector option:selected').text(),
                            placeholder_value: $('#placeholder-selector option:selected').val(),
                            compare_name: $("#placeholder-compare").val(),
                            compare_with: $("#compare-with").val(),
                            dann_text: $("#dann-text").val(),
                            sonst_text: $("#sonst-text").val(),
                            branch_id: $("#communication_template_branch_id").val()
                        };
                        // and send a ajax request to save the new placeholder.
                        var _dataform = new FormData();
                        _dataform.append('placeholder_data', JSON.stringify(_data));
                        if (form_validation()) {
                            show_loading_div({loading_text: I18n.patient.save});
                            $.ajax({
                                url: "/communication/templates/save_placeholder",
                                type: "POST",
                                data: _dataform,
                                sync: false,
                                processData: false,
                                contentType: false,
                                success: function (data) {
                                    send_to_parent(success_notice(data[0].status));
                                    $("#new-placeholder-name").val('');
                                    $('#placeholder-selector').prop('selectedIndex', 0);
                                    $("#compare-with").val('');
                                    $("#dann-text").val('');
                                    $("#sonst-text").val('');
                                    $.modal.close();
                                    CKEDITOR.instances['smstextarea'].destroy();
                                    CKEDITOR.instances['mailtextarea'].destroy();
                                    init_editors();
                                    hide_loading_div();

                                },
                                error: function (data) {
                                    send_to_parent(error_notice(data.responseJSON[0].status));
                                    hide_loading_div();

                                }
                            });
                        } else {
                            send_to_parent(error_notice("Eingabefelder dürfen nicht leer sein"));
                        }
                        remove_red_border_after_validation();
                    });

                    $("#placeholder-del-btn").off().click(function (e) {
                        var parent_div = $(this).closest("#placeholder_div");
                        var delete_placeholder_id = parent_div.find("#existing_placeholder_db_id").val();
                        if (delete_placeholder_id > 0) {
                            show_loading_div({loading_text: I18n.patient.save});
                            $.ajax({
                                url: "/communication/templates/delete_existing_placeholder",
                                type: "POST",
                                data: {delete_placeholder_id: delete_placeholder_id},
                                dataType: 'json',
                                success: function (data) {
                                    send_to_parent(success_notice(data[0].status));
                                    $.modal.close();
                                    CKEDITOR.instances['smstextarea'].destroy();
                                    CKEDITOR.instances['mailtextarea'].destroy();
                                    init_editors();
                                    hide_loading_div();

                                },
                                error: function (data) {
                                }
                            })
                        }
                    });

                    // on change or keyup remove the red border
                    function remove_red_border_after_validation() {
                        $(".form-inputs").on("keyup change", function (e) {
                            $(this).css("border", "solid 1px green");
                            $(this).removeClass("invalid");
                        });
                    }

                    // function to validate the form
                    function form_validation() {
                        var val = 0;
                        $(".main-container").find(".form-inputs").each(function (element, index) {
                            if ($(this).prop('required')) {
                                if ($(this).is(":visible")) {
                                    if ($(this).val() === '') {
                                        $(this).css('border', 'solid 1px red');
                                        $(this).addClass('invalid');
                                        val++;
                                    }
                                }
                            }
                        });
                        if (val === 0) {
                            return true;
                        } else {
                            return false;
                        }
                    }
                });
            });
````
<sup align="right"><a href="#table-of-contents">Go to top</a></sup>

## Backend Handling with Ruby

Default placeholder as Ruby Array

````ruby
R:\app\config\initializers\constants\ckeditor_placeholders.rb

CKEDITOR_PLACEHOLDERS = [
    ['__HDL_NAME__', 'Händler', 'Händlername'],
    ['__WP_AUTO__', 'Auto-Bezeichnung', 'Auto'],
    ['__WP_KENNZ__', 'Kennzeichen', 'Kennzeichen'],
    ['__BENUTZER_NAME__', 'Benutzername'],
    ['__WP_DATUM__', 'Termindatum', 'Termindatum'],
    ['__WP_ZEIT__', 'Terminzeit', 'Terminzeit'],
    ['__ABHOLDATUM__', 'Abholdatum', 'Abholdatum'],
    ['__ABHOLZEIT__', 'Abholzeit', 'Abholzeit'],
    ['__ERSATZWAGEN__', 'Ersatzwagen', 'Ersatzwagen'],
    ['__EINGRIFFE_BLOCK__', 'Eingriffe'],
    ['__EINGRIFFE_FORTLAUFEND__', 'Eingriffe fortlaufend'],
    ['__KUNDENBERATER__', 'Kundenberater'],
    ['__MARKE__', 'Marke']
]
````
Get all Placeholders (Default + custom placeholders if exist).

````ruby
  def get_placeholders
    appointment_id = params[:appointment_id]
    if appointment_id.nil? || appointment_id.to_i < 1
      # grab default ckeditor placholder
      default_placeholder = CKEDITOR_PLACEHOLDERS
      default_placeholder_as_string = JSON.generate(CKEDITOR_PLACEHOLDERS)
      # also check the custom placeholder
      check_custom_placeholder = PlaceholderSetting.all
      # if custom placeholder exist then add into default placeholder
      if check_custom_placeholder
        check_custom_placeholder.each do |single|
          if default_placeholder_as_string.include? single.custom_placeholder_value
          else
            new_array = [single.custom_placeholder_value, single.custom_placeholder_name, single.id]
            default_placeholder.push(new_array)
          end
        end
      end
      placeholders = JSON.generate(CKEDITOR_PLACEHOLDERS)
    else
      placeholders = translate_placeholders(text:      JSON.generate(CKEDITOR_PLACEHOLDERS), appointment_id: params[:appointment_id],
                                            branch_id: params[:branch_id], user_id: params[:user_id])
    end

    render :json => JSON.parse(placeholders)
  end
````
Save new custom placeholder or update existing placeholder to the DB

````ruby
 def save_placeholders
    # initial empty array to store success and error
    error_occured = []
    success_data = []
    # grab the form parameters
    placeholder_data = JSON.parse(params[:placeholder_data])
    # if parameters received then create a new placeholder
    if placeholder_data['placeholder_db_id'].to_i > 0
      begin
        CKEDITOR_PLACEHOLDERS.delete_if{|x| x[2].to_s === placeholder_data['placeholder_db_id'].to_s}
        new_placeholder_value = "__" + placeholder_data['new_placeholder_name'].parameterize.underscore.upcase + "__"
        update_existing_placeholder = PlaceholderSetting.find_by(id: placeholder_data['placeholder_db_id']).update(
            custom_placeholder_name:    placeholder_data['new_placeholder_name'],
            custom_placeholder_value:   new_placeholder_value,
            placeholder_name:           placeholder_data['placeholder_name'],
            placeholder_value:          placeholder_data['placeholder_value'],
            compare_value:              placeholder_data['compare_name'],
            compare_with:               placeholder_data['compare_with'],
            dann_text:                  placeholder_data['dann_text'],
            sonst_text:                 placeholder_data['sonst_text']
        )
        if update_existing_placeholder
          success_data.push({
                                status:   "Platzhalter erfolgreich aktualisiert"
                            })
        end
      rescue Exception => e
        error_occured.push(:error_msh => "Datensatz nicht aktualisiert.")
        puts e.to_s
      end
    else
      begin
        new_placeholder_value = "__" + placeholder_data['new_placeholder_name'].parameterize.underscore.upcase + "__"
        # check the placeholder already exists or not
        if PlaceholderSetting.where(
            custom_placeholder_name:    placeholder_data['new_placeholder_name'],
            custom_placeholder_value:   new_placeholder_value,
            branch_id:                  placeholder_data['branch_id']).present?
          error_occured.push({status: "Platzhalter ist bereits vorhanden"})
        else
          # if dose not exist create a new placeholder
          create_new_placeholder = PlaceholderSetting.create(
              branch_id:                placeholder_data['branch_id'],
              custom_placeholder_name:  placeholder_data['new_placeholder_name'],
              custom_placeholder_value: new_placeholder_value,
              placeholder_name:         placeholder_data['placeholder_name'],
              placeholder_value:        placeholder_data['placeholder_value'],
              compare_value:            placeholder_data['compare_name'],
              compare_with:             placeholder_data['compare_with'],
              dann_text:                placeholder_data['dann_text'],
              sonst_text:               placeholder_data['sonst_text'])
          if create_new_placeholder
            success_data.push({
                                  saved_placeholder_name:   create_new_placeholder['custom_placeholder_name'],
                                  saved_placeholder_value:  create_new_placeholder['custom_placeholder_value'],
                                  status:                   "Platzhalter erfolgreich gespeichert"
                              })
          end
        end

      rescue Exception => e
        error_occured.push(:error_msh => "Datensatz nicht erstellt.")
        puts e.to_s
      end
    end

    if error_occured.length > 0
      # then render head not found
      render :status => :not_found, :json => error_occured
    else
      # otherwise send ok status
      render :json => success_data
    end
  end
````

Delete custom existing placeholder

````ruby
def delete_existing_placeholder
    error_occured = []
    success_date = []
    if params[:delete_placeholder_id].to_i > 0
      begin
        CKEDITOR_PLACEHOLDERS.delete_if{|x| x[2].to_s === params['delete_placeholder_id'].to_s}
        delete_placeholder = PlaceholderSetting.find_by(id: params[:delete_placeholder_id].to_i).destroy
        if delete_placeholder
          success_date.push({
                                status: "Platzhalter erfolgreich gelöscht"
                            })

        else
          success_date.push({
                                status: "Platzhalter nicht gelöscht"
                            })
        end
      rescue Exception => e
        error_occured.push(:err_msh => "Something went wrong")
        puts e.to_s
      end
    end

    if error_occured.length > 0
      render :status => :not_found, :json => error_occured
    else
      render :json => success_date
    end
  end
  ````





<sup align="right"><a href="#table-of-contents">Go to top</a></sup>

<!-- CONTACT -->

## Contact

Rahman Habib - [habib2030@web.de](mailto:habib2030@web.de)

LinkedIn Profile: [smhabibjr](https://www.linkedin.com/in/smhabibjr)

Youtube Channel: [HabibJr](https://www.youtube.com/c/HabibJr)

Facebook Profile: [Habib Jr](https://www.facebook.com/smhabibjr)

Website : [habibjr.com](https://www.habibjr.com)

<sup align="right"><a href="#table-of-contents">Go to top</a></sup>
