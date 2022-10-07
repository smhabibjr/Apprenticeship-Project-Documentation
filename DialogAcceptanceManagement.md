## Table of Contents

- [Project Description](#project-description)
- [Technologies and Programing Languages](#technologies-and-programming-languages)
- Frontend Design
  - [Modal Dialog from php](#modal-dialog-from-php)
  - [Frontend Design with HAML and Bootstrap](#frontend-design-with-haml-and-bootstrap)
  - [Form validation using JavaScript](#form-validation-using-javascript)
- Backend
  - [Backend Handling with Ruby](#backend-handling-with-ruby)
- [Contact](#contact)

## Dialog Acceptance Management
![image](https://user-images.githubusercontent.com/77357735/183630682-b29d1a14-15ff-4140-b176-66449d9d9244.png)


## Project Description

## Technologies and Programming Languages

Frontend: [HAML](https://haml.info/), [Bootstrap](https://getbootstrap.com/), [JavaScript](https://www.javascript.com/)

Backend: [Ruby](https://www.ruby-lang.org/de/), [PHP](https://www.php.net)

Framework: [Ruby on Rails](https://rubyonrails.org/)


## Modal Dialog from php

Initialize modal box size : index.php

````php
    //2022-03-11 Verwaltung von Dialoggannahmen in Tagesansicht
    echo "<font class=\"content\"><b>Dialogannahmenverwaltung</b>";
    $modal = new modalDialog();
    $pref_mod = array(
        "height" => "660px",
        "width" => "1000px",
        "reload" => false,
        "close-question" => true
    );
    echo $modal->create("link_js", "[bearbeiten]", "modules/Werkstatt/dialogannahme/dialog_acceptance_management.php", $pref_mod);
    echo " </font><br>";
````


/dialog_acceptance_management.php

```php
`<?php
/**
 * Created by PhpStorm.
 * User: Habib
 * Date: 11.03.2022
 * Time: 12:42
 */

chdir(dirname(__FILE__) . "/../../../");
include_once("mainfile.php");
require_once "modules/Werkstatt/wterm15/modalDialog_class.php";

// collect all info about hdl using get_hdl_array function and store it inside hdl_array
$hdl_array = get_hdl_array();

$urls = array();
foreach ($hdl_array as $hdl) {
    // send a http requst to the odbcrunner wiht hdl_id
    $urls["$hdl[ort]<span style='display:none'>$hdl[firma]$hdl[filiale]</span></br>$hdl[straße]"] = "http://". $_SERVER["HTTP_HOST"].":5050/dialog_management/$hdl[id]";
    $hdl_array = get_hdl_array();
}
// initialize a content height in model
$prefs = array(
    "tab_content_height" => 500,
    "close-question" => true
);
// initialize a new model with model title and icon
$modal = new modalDialog(
    "Verwaltung Dialogannahmen",
    "/eins/html/images/1-wtp_icons_ci/1-wtp-mit-Pyramide.png",
    NULL,
    $urls,
    NULL,
    $prefs
);
$modal->create("html");
```
<sup align="right"><a href="#table-of-contents">Go to top</a></sup>

## Frontend Design with HAML and Bootstrap

````haml
`.container-fluid
  %form#dialog_management_acceptence
    .container-fluid
      = hidden_field_tag :firma, @dialog_management_data[:firma], class:"hdl_firma"
      = hidden_field_tag :filiale, @dialog_management_data[:filiale], class:"hdl_filiale"
      = hidden_field_tag :branch_id, @dialog_management_data[:branch_id], class:"branch_id"
      .row.ml-1
        .col-6
          .row.p-1
            Verwaltung von Dialogannahmen aktivieren
          .row.p-1.d-flex.align-items-center
            Anzahl an verfügbaren Dialogannahmen je Zeitslot
        .col-6
          .row.p-1
            = check_box_tag  "dialogannahmen_active_status", '' , @dialog_management_data[:dialogannagmen_status].wie_onoff == 1 ? true : false , {:id => "dialogannahmenAktivierenCheckbox"}
          .row.w-25
            = text_field_tag "dialog_time_slot", @dialog_management_data[:dialogannagmen_status].wie_todo, {:class => "form-control dialogannahmenAktivierenTimeSlot",:autocomplete=>"off"}

      .row
        .col.d-flex.align-items-center.ml-2
          Datum(Von)
        .col.d-flex.align-items-center
          Uhrzeit(Von)
        .col.d-flex.align-items-center
          Datum(bis)
        .col.d-flex.align-items-center
          Uhrzeit(bis)
        .col
          Abzug von verfügbaren Dialogannahmen
        .col.d-flex.justify-content-center.align-items-center
          Löschen

    .container-fluid.mb-2.dialog-container#dialog-container{:style => "height:265px; position:relative"}
      - internal_frontend_id = 1
      - @dialog_management_data[:dialogueacceptances].each do |single_dialogacceptance|
        .row.p-1.dialogannahmen-rows
          .col
            = text_field_tag "date_from[]", single_dialogacceptance.date_from.strftime("%d.%m.%Y"), :class => "form-control input-field date-from-field date_from",:id => "date_from_field_#{internal_frontend_id}", :pattern=>"(0[1-9]|1[0-9]|2[0-9]|3[01]).(0[1-9]|1[012]).[0-9]{4}", :readonly => "readonly",:required => true , :autocomplete=>"off"
          .col
            = text_field_tag "time_from[]", single_dialogacceptance.time_from.strftime("%H:%M"), :class => "form-control input-field time-from-field start_timepicker", :id => "time_from_field_#{internal_frontend_id}",:required => true ,:readonly => "readonly"
          .col
            = text_field_tag "date_to[]", single_dialogacceptance.date_to.strftime("%d.%m.%Y"), :class => "form-control input-field date-to-field date_to", :id => "date_to_field_#{internal_frontend_id}",:pattern=>"(0[1-9]|1[0-9]|2[0-9]|3[01]).(0[1-9]|1[012]).[0-9]{4}", :readonly => "readonly", :required => true,:autocomplete=>"off"
          .col
            = text_field_tag "time_to[]", single_dialogacceptance.time_to.strftime("%H:%M"), :class => "form-control input-field time-to-field end_timepicker", :id => "time_to_field_#{internal_frontend_id}",:required => true ,:readonly => "readonly"
          .col
            = text_field_tag "minus[]", single_dialogacceptance.minus, :class => "form-control input-field dialogannahmen_minus", :id => "minus_#{internal_frontend_id}",:required => true, :autocomplete=>"off"
          .col.d-flex.justify-content-center
            .btn.btn-danger.delBtn.border-0.rounded-0{:id => "delBtn_#{internal_frontend_id}", :style => "background:rgb(255, 156, 56);"}
              %i.fa.fa-trash
          = hidden_field_tag "dialogannahmen_internal_frontend_id[]", internal_frontend_id, class:"dialogannahmen_internal_frontend_id internal_id_#{internal_frontend_id}"
          = hidden_field_tag "dialogannahmen_db_id[]", single_dialogacceptance.id, class:"dialogannahmen_db_id"
          - internal_frontend_id += 1

    .row.d-flex.justify-content-center.mb-1
      .col.d-flex.justify-content-center
        .btn.btn-primary.border-0.rounded-0#btn_add{:style => "background:rgb(255, 156, 56);"}
          %i.fa.fa-plus
    .row
      .col.d-flex.justify-content-center
        = button_tag :Speichern,:id => "submit_dailogannahe", :class => "btn btn-primary border-0 rounded-0", type: :submit, :style => "background:rgb(255, 156, 56); font-weight: bold; outline:0px !important; box-shadow: none !important;"

=render :partial => 'shared/confirm', locals: {name: "confirm_delete_dialogannahme", title: "Soll diese Dialogannahme wirklich gelöscht werden?", width: "400px;", height: "120px" }


.row.p-1.dialogannahmen-new-row-template.d-none
  .col
    = text_field_tag "date_from[]", '', :class => "form-control input-field date-from-field",:pattern=>"(0[1-9]|1[0-9]|2[0-9]|3[01]).(0[1-9]|1[012]).[0-9]{4}", :readonly => "readonly",:required => true, :autocomplete=>"off"
  .col
    = text_field_tag "time_from[]", '', :class => "form-control input-field time-from-field",:required => true ,:readonly => "readonly"
  .col
    = text_field_tag "date_to[]", '', :class => "form-control input-field date-to-field",:pattern=>"(0[1-9]|1[0-9]|2[0-9]|3[01]).(0[1-9]|1[012]).[0-9]{4}", :readonly => "readonly",:required => true, :autocomplete=>"off"
  .col
    = text_field_tag "time_to[]",'', :class => "form-control input-field time-to-field",:required => true, :readonly => "readonly"
  .col
    = text_field_tag "minus[]", '', :class => "form-control input-field dialogannahmen_minus",:required => true, :autocomplete=>"off"
  .col.d-flex.justify-content-center
    .btn.btn-danger.delBtn.border-0.rounded-0{:style => "background:rgb(255, 156, 56);"}
      %i.fa.fa-trash
  = hidden_field_tag "dialogannahmen_internal_frontend_id[]", 0, class:"dialogannahmen_internal_frontend_id"
  = hidden_field_tag "dialogannahmen_db_id[]", 0, class:"internal_db_id"
````

<sup align="right"><a href="#table-of-contents">Go to top</a></sup>

## Form validation using JavaScript

````javascript
$('.dialog_management.index').ready(function () {
    // add new class when the data will be changed
    function addclass_when_data_changed(){
        // on changed on input field.
        $(".input-field").on('change',function () {
            // go to their parents row and add 'changed-row' to identify row has been changed.
            var main_element = $(this).closest(".dialogannahmen-rows").addClass("changed-row");
        });
    }

    // date and time validation function
    function date_time_validation(selector) {
        // go to the main row
        var main_element = selector.closest(".dialogannahmen-rows");
        // grab the start date and convert into in german format.
        var start_date = convert_german_date_to_iso(main_element.find(".date_from").val());
        // grab the end date and convert into in german format.
        var end_date = convert_german_date_to_iso(main_element.find(".date_to").val());
        // grab the start time.
        var start_time = main_element.find(".start_timepicker").val();
        // grab the end time.
        var end_time = main_element.find(".end_timepicker").val();
        // create a date and time parse.
        var start_date_time = start_date + "T" + start_time + ":00";
        var end_date_time = end_date + "T" + end_time + ":00";
        // check end dateTime bigger than start dateTime or not.
        if (start_date_time > end_date_time) {
            // if start dateTime bigger then end dateTime , show notification.
            send_to_parent(error_notice("Von Datum und Zeit dürfen nicht nach Bis Datum und Zeit liegen"));
            // and also submit button will be disabled
            $(':input[type="submit"]').prop('disabled', true);
            // returns function value is equels 1
            return 1;
        } else {
            // otherwise submit button will be enable to click.
            $(':input[type="submit"]').prop('disabled', false);
            // returns value will the 0
            return 0;
        }
    }

    // blind start date picker.
    function bind_date_from_picker() {
        // select the date-input-fields
        var selector = $('.date_from');
        // initialize the date picker to the selector.
        selector.datepicker({
            beforeShow: function () {
                // grab the current date and time
                var limit_min_date = new Date();
                // grab only date and set it into 'limit_min_date'
                limit_min_date.setDate(limit_min_date.getDate());
                // set min date
                $(this).datepicker('option', 'minDate', limit_min_date);
            },
            onClose: function () {
                // on close run the date_time_vallidation function to validate date and time.
                var status = date_time_validation($(this));
                // if validation is oky then remove the red border form input fields.
                if (status === 0) {
                    $(this).removeClass("border border-danger");
                    $(this).closest(".dialogannahmen-rows").find(".date_to").removeClass("border border-danger");
                }
            }
        });
    }

    // blind end date picker.
    function bind_date_to_picker() {
        // select the date-input-fields
        var selector = $('.date_to');
        // initialize the date picker to the selector.
        selector.datepicker({
            showOn: 'both',
            // before show the date calender
            beforeShow: function () {
                // go to parent row and grab start date.
                var parent_row = $(this).closest('.dialogannahmen-rows');
                var start_date = parent_row.find('.date_from');
                var limit_min_date = new Date($(start_date).datepicker('getDate'));
                // before start date all date will be disabled
                $(this).datepicker('option', 'minDate', limit_min_date);
            },
            onClose: function () {
                // on close check the date and time.
                var status = date_time_validation($(this));
                // if date and time oky then remove red border from them.
                if (status === 0) {
                    $(this).removeClass("border border-danger");
                    $(this).closest(".dialogannahmen-rows").find(".date_from").removeClass("border border-danger");
                }
            }
        });
    }

    // blind start time picker.
    function bind_start_timepicker() {
        // select the date-input-fields
        var selector = $('.start_timepicker');
        // initialize the time picker to the selector.
        selector.timepicker({
            rows: 2,
            showPeriodLabels: false,
            minuteText: 'Minute',
            hourText: 'Stunde',
            closeText: 'Fertig',
            closeButtonText: 'Schließen',
            showCloseButton: true,
            minutes: {
                starts: 0,
                interval: 5
            },
            //stunden anzeige
            hours: {
                starts: 5,
                ends: 22
            },
            onClose: function () {
                // on close run the date_time_validation function to check date and time.
                var status = date_time_validation($(this));
                // if time oky then remove the red border from them.
                if (status === 0) {
                    $(this).removeClass("border border-danger");
                    $(this).closest(".dialogannahmen-rows").find(".end_timepicker").removeClass("border border-danger");
                }
            },
        });
    }
    // blind end time picker.
    function bind_end_timepicker() {
        // select the date-input-fields
        var selector = $('.end_timepicker');
        // initialize the time picker to the selector.
        selector.timepicker({
            rows: 2,
            showPeriodLabels: false,
            minuteText: 'Minute',
            hourText: 'Stunde',
            closeText: 'Fertig',
            closeButtonText: 'Schließen',
            showCloseButton: true,
            minutes: {
                starts: 0,
                interval: 5
            },
            //stunden anzeige
            hours: {
                starts: 5,
                ends: 22
            },
            onClose: function () {
                // on close run the date_time_validation function to check date and time.
                var status = date_time_validation($(this));
                if (status === 0) {
                    // if time oky then remove the red border from them.
                    $(this).removeClass("border border-danger");
                    $(this).closest(".dialogannahmen-rows").find(".start_timepicker").removeClass("border border-danger");
                }
            }
        });
    }

    // blind date function
    function bind_delete_dialogannahme() {
        // select the delete button
        var delete_selector = $(".delBtn");
        // after click the delete button
        delete_selector.click(function (e) {
            // go to the main row
            var main_element = $(this).closest(".dialogannahmen-rows");
            // and grab the db_id of it.
            var deleted_db_id = main_element.find(".dialogannahmen_db_id").val();
            // if db_id exist then send an ajax request to delete that row
            if (parseInt(deleted_db_id) > 0) {
                // then open the confirm box.
                open_confirm_box($('.confirm_delete_dialogannahme'));
                // if confirmed
                $(".button_true_confirm_delete_dialogannahme").off().click(function () {
                    // then send an ajax request to the controller.
                    $.ajax({
                        url: "/dialog_management/" + deleted_db_id,
                        type: "DELETE",
                        success: function () {
                            // after success the delete request
                            // remove the whole row
                            main_element.remove();
                            // also send a delete notice to the browser.
                            send_to_parent(delete_notice());
                            // and close the confirm box
                            $.modal.close();
                        },
                        error: function () {
                            send_to_parent(error_notice());
                        }
                    });
                });
            } else {
                // otherwise only remove the whole element.
                main_element.remove();
            }
        });
    }
    // submit function
    $("#dialog_management_acceptence").submit(function (e) {
        e.preventDefault();
        // disabled the all row except (changed-row) where changes have been made.
        $('.dialogannahmen-rows').not(".changed-row").find('input').prop('disabled', true);
        // serialize the form and save all data inside 'form_data'
        let form_data = $(this).serialize();
        // send a post request using ajax
        $.ajax({
            url: "/dialog_management",
            data: form_data,
            type: "POST",
            success: function (data) {
                // after success the post request enable the all input fields.
                $('.dialogannahmen-rows').find('input').prop('disabled', false);
                $('.dialogannahmen-rows').removeClass('changed-row');
                // send success notification to the browsers.
                send_to_parent(success_notice());
                // check the json data exist or not.
                if (data.length > 0) {
                    // if exist then loop the data array through every single element and change the db_id
                    for (var i = 0; i < data.length; i++) {
                        $("input[name='dialogannahmen_internal_frontend_id[]'][value='" + data[i].internal_id + "']").parent().find("input[name='dialogannahmen_db_id[]']").val(data[i].db_id)
                    }
                }
            },
            error: function (data) {
                // if error occuerd then grab that error
                var error_response = JSON.parse(data.responseText);
                if (error_response.length > 0) {
                    // loop the error_response array through every sinle element
                    for (var i = 0; i < error_response.length; i++) {
                        // grab internal_id
                        var _response_internal_id = error_response[i].internal_id;
                        // and also grab the error mag where error_occured exactly.
                        var _response_error_msh = error_response[i].error_msh;
                        // go to the main row according to internal_id
                        var _main_element = $("input[name='dialogannahmen_internal_frontend_id[]'][value='" + _response_internal_id + "']").closest(".dialogannahmen-rows");
                        // check the error msg
                        if (_response_error_msh === "Uhrzeit von liegt nach Uhrzeit bis") {
                            // if error mag reletade to time then add red border for time-input-fields
                            _main_element.find(".start_timepicker").addClass("border border-danger");
                            _main_element.find(".end_timepicker").addClass("border border-danger");
                        } else if (_response_error_msh === "Datum von liegt nach Datum bis") {
                            // if error mag reletade to date then add red border for date-input-fields
                            _main_element.find(".date_from").addClass("border border-danger");
                            _main_element.find(".date_to").addClass("border border-danger");
                        }
                    }
                }
                // also send error notice to the browser.
                send_to_parent(error_notice());
            }
        });
    });

    // add perfect scrollbar for content div
    const container = document.querySelector('.dialog-container');
    const ps = new PerfectScrollbar(container);
    // initialize lc switch for checkbox and alternative text
    $("#dialogannahmenAktivierenCheckbox").lc_switch('Ja', 'Nein');
    // create a dynamic id for new row. without unique id date and time picker doesnt pick date and time
    var last_row = $("#dialog-container").children(".dialogannahmen-rows").last();
    var last_row_value = parseInt(last_row.find(".dialogannahmen_internal_frontend_id").val());
    var increment_last_row_value = 0;
    if (last_row.length > 0) {
        increment_last_row_value = last_row_value + 1
    } else {
        increment_last_row_value = 1
    }
    // clone a new row when add button clicked
    $("#btn_add").click(function () {
        var newField = $(".dialogannahmen-new-row-template").clone().removeClass("dialogannahmen-new-row-template d-none").addClass("dialogannahmen-rows");
        newField.find(".dialogannahmen_internal_frontend_id").val(increment_last_row_value);
        newField.find(".date-from-field").attr("id", "date_from_field_" + increment_last_row_value).addClass("date_from");
        newField.find(".date-to-field").attr("id", "date_to_field_" + increment_last_row_value).addClass("date_to");
        newField.find(".time-from-field").attr("id", "time-from-field_" + increment_last_row_value).addClass("start_timepicker");
        newField.find(".time-to-field").attr("id", "time-to-field_" + increment_last_row_value).addClass("end_timepicker");
        newField.find(".internal_db_id").addClass("dialogannahmen_db_id");
        $("#dialog-container").append(newField);
        increment_last_row_value += 1;
        // scroll ganz bottom when new row added
        scroll_to_bottom_of('#dialog-container');
        addclass_when_data_changed();
        //runn date picker
        bind_date_from_picker();
        bind_date_to_picker();
        // run time picker
        bind_start_timepicker();
        bind_end_timepicker();
        // run delete function
        bind_delete_dialogannahme();
    });
    addclass_when_data_changed();
    //runn date picker
    bind_date_from_picker();
    bind_date_to_picker();
    // run time picker
    bind_start_timepicker();
    bind_end_timepicker();
    // run delete function
    bind_delete_dialogannahme();
});
````
<sup align="right"><a href="#table-of-contents">Go to top</a></sup>

## Backend Handling with Ruby

Mapping: config/routes.rb

````ruby
resources :dialog_management, only: [:index, :create, :destroy]
  get '/dialog_management/:branch_id' => 'dialog_management#index'
````

Controller: app/controllers/brand_management.rb

````ruby
class DialogManagementController < ApplicationPureBootstrapController
  require 'odbc_runner/settings_helper.rb'

  def index
    # grab all data about branch
    branch = get_branch_by_branch_id(params[:branch_id])
    # create a hash with branch data and dialogannahe data
    @dialog_management_data = {
        :firma => branch.hdl_firma,
        :filiale => branch.hdl_filiale,
        :branch_id => params[:branch_id],
        :dialogannagmen_status => Setting.find_by(wie_module: "Verwaltung Dialogannahmen aktiviert", wie_firma: branch.hdl_firma, wie_filiale: branch.hdl_filiale),
        :dialogueacceptances => DialogueAcceptance.where(firma: branch.hdl_firma, filiale: branch.hdl_filiale)
    }
  end

  def create
    error_occured = []
    saved_data = []
    # if dialogannahmen_active_status param exist or not
    if params.include? 'dialogannahmen_active_status'
      dialogannahme_active_status = 1
    else
      dialogannahme_active_status = 0
    end
    # check time_slot parameter exist or not.
    if params.include? 'dialog_time_slot'
      # check time_slot value exist or not .
      if params[:dialog_time_slot] === ""
        # if does not exist then set a default value as 0
        dialog_time_slot = '0'
      else
        # otherwise pramas value.
        dialog_time_slot = params[:dialog_time_slot]
      end
      #  check firma and filiale exist or not.
      if params[:firma] and params[:filiale].present?
        # if exist dann update the status and time_slot value.
        aktive_status_updated = Setting.find_by(
            wie_module: "Verwaltung Dialogannahmen aktiviert",
            wie_firma: params[:firma],
            wie_filiale: params[:filiale]).update(
            wie_onoff: dialogannahme_active_status,
            wie_todo: dialog_time_slot)
        unless aktive_status_updated
          # unless update, returns ar error.
          error_occured.push(1)
        end
      end
    end
    # check internal_frontend_id exist or not.
    if params[:dialogannahmen_internal_frontend_id].present?
      # if exist then loop it with index and value.
      params[:dialogannahmen_internal_frontend_id].each_with_index do |val, index|
        if val.to_i > 0
          # if db_id exist then check every parameters has value
          if params[:date_from][index] != "" and params[:time_from][index] != "" and params[:date_to][index] != "" and params[:time_to][index] != "" and params[:minus][index] != ""
            # check the maximum time range.
            if params[:time_from][index] <= "22:55" and params[:time_to][index] <= "22:55"
              # create a date time parse.
              start_date_time = Date.parse(params[:date_from][index]).to_s + "T" + params[:time_from][index] + ":00"
              end_date_time = Date.parse(params[:date_to][index]).to_s + "T" + params[:time_to][index] + ":00"
              # check end date time bigger than start date time or not.
              if end_date_time > start_date_time
                # check db_id exist or not.
                if params[:dialogannahmen_db_id][index].to_i > 0
                  # update the existing record
                  begin
                    DialogueAcceptance.find_by(id: params[:dialogannahmen_db_id][index]).update(
                        date_from: params[:date_from][index],
                        time_from: params[:time_from][index],
                        date_to: params[:date_to][index],
                        time_to: params[:time_to][index],
                        minus: params[:minus][index],
                        firma: params[:firma],
                        filiale: params[:filiale])
                  rescue Exception => e
                    # unless save push an error_occured
                    error_occured.push({:internal_id => val, :error_msh => "Datensatz wird nicht aktualisiert."})
                    puts e.to_s
                  end
                  #   if db_id does not exist then create a new record
                else
                  begin
                    # create a new record.
                    create_new_dialogannahme = DialogueAcceptance.create(
                        date_from: params[:date_from][index],
                        time_from: params[:time_from][index],
                        date_to: params[:date_to][index],
                        time_to: params[:time_to][index],
                        minus: params[:minus][index],
                        firma: params[:firma],
                        filiale: params[:filiale])
                    saved_data.push({internal_id: val, db_id: create_new_dialogannahme['id']})
                  rescue Exception => e
                    error_occured.push({:internal_id => val, :error_msh => "Datensatz nicht erstellt."})
                    puts e.to_s
                  end
                end
              else
                # if inputted date are not valid than return an error msg for date input field
                if Date.parse(params[:date_to][index]) < Date.parse(params[:date_from][index])
                  error_occured.push({:internal_id => val, :error_msh => "Datum von liegt nach Datum bis"})
                end
                # if inputted time are not valid than return an errors for time input fields.
                if Date.parse(params[:date_to][index]) == Date.parse(params[:date_from][index])
                  if params[:time_to][index] < params[:time_from][index]
                    error_occured.push({:internal_id => val, :error_msh => "Uhrzeit von liegt nach Uhrzeit bis"})
                  end
                end
              end
            else
              error_occured.push({:internal_id => val, :error_msh => "Ungültiger Zeitbereich."})
            end
          end
        end
      end
    end

    # if occurred happend
    if error_occured.length > 0
      # then render head not found
      render :status => :not_found, :json => error_occured
    else
      # otherwise send ok status
      render json: saved_data
    end
  end

  def destroy
    if params[:id].present?
      # if exist then destroy it from the db
      deleted_record = DialogueAcceptance.find_by(id: params[:id]).destroy
      if deleted_record
        head :ok
      else
        head :not_found
      end
    end
  end

end

````

<sup align="right"><a href="#table-of-contents">Go to top</a></sup>


## Contact

Rahman Habib - [habib2030@web.de](mailto:habib2030@web.de)

LinkedIn Profile: [smhabibjr](https://www.linkedin.com/in/smhabibjr)

Youtube Channel: [HabibJr](https://www.youtube.com/c/HabibJr)

Facebook Profile: [Habib Jr](https://www.facebook.com/smhabibjr)

Website : [habibjr.com](https://www.habibjr.com)

<sup align="right"><a href="#table-of-contents">Go to top</a></sup>
