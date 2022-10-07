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

![image](https://user-images.githubusercontent.com/77357735/194566166-cd67b4e2-0c08-4f1d-8303-60a839508d6f.png)

## Project Description

## Technologies and Programming Languages

Frontend: [HAML](https://haml.info/), [Bootstrap](https://getbootstrap.com/), [JavaScript](https://www.javascript.com/)

Backend: [Ruby](https://www.ruby-lang.org/de/), [PHP](https://www.php.net)

Framework: [Ruby on Rails](https://rubyonrails.org/)


## Modal Dialog from php

Initialize modal box size : index.php

````php
       echo "<font class=\"content\"><b>Feiertagsverwaltung rails</b>";
    $modal = new modalDialog();
    $pref_mod = array(
        "height" => "700px",
        "width" => "1100px",
        "reload" => false,
        "close-question" => true
    );
    echo $modal->create("link_js", "[bearbeiten]", "modules/Werkstatt/feiertage/feiertage_rails.php", $pref_mod);
    echo " </font><br>";
````


/feiertage/feiertage_rails.php

```php
<?php
/**
 * Created by PhpStorm.
 * User: Habib
 * Date: 25.10.2021
 * Time: 15:19
 */

chdir(dirname(__FILE__) . "/../../../");
include_once ("mainfile.php");
require_once "modules/Werkstatt/wterm15/modalDialog_class.php";



$uid = get_uid();

// Festlegung der Variablen innerhalb der einzelnen Seiten
// Überprüfe welche Rechte der Benutzer hat und zeige je nachdem die freigegebenen Tabs an
$sql = "SELECT * FROM can_hdlid" .
    " LEFT OUTER JOIN can_extended_rights " .
    "ON can_hdlid.hdl_firma=can_extended_rights.firma " .
    "AND can_hdlid.hdl_filiale=can_extended_rights.filiale " .
    "WHERE ((can_extended_rights.category='Administrator' AND can_extended_rights.name='Administrator(filialübergreifend)') ".
    "OR (can_extended_rights.category='Administrator' AND can_extended_rights.name='Hauptbenutzer')) AND can_extended_rights.on_off > 0 ".
    "AND can_extended_rights.user_id=$uid AND hdl_passiv=0 ORDER BY can_hdlid.hdl_id";
$res=mysql_query($sql,$dbi);

if (mysql_num_rows($res) > 0) {
    while ($row = mysql_fetch_array($res)) {
        $hdlid_array[] = array(
            "hdl" => "$row[hdl_konto]",
            "id" => "$row[hdl_id]",
            "firma" => "$row[hdl_firma]",
            "filiale" => "$row[hdl_filiale]",
            "name" => $row['hdl_name'],
            "name2" => $row['hdl_name2'],
            "ort" => $row['hdl_ort'],
            "straße" => $row['hdl_str']
        );
    }
}

$urls = array();
foreach ($hdlid_array as $hdl) {
    $urls["$hdl[ort]<span style='display:none'>$hdl[firma]$hdl[filiale]</span></br>$hdl[straße]"] = "http://" . $_SERVER["HTTP_HOST"] . ":5050/holidays/$hdl[id]";

}

$prefs = array(
    "title" => NULL,
    "body_height" => "600px",
    "tab_content_height" => 500,

);

$modal = new modalDialog(
    "Feiertagsverwaltung",
    "/eins/html/images/1-wtp_icons_ci/Pikto-acs-07.png",
    NULL,
    $urls,
    null,
    $prefs
);
$modal->create("html");
```
<sup align="right"><a href="#table-of-contents">Go to top</a></sup>

## Frontend Design with HAML and Bootstrap

````haml
.containers
  .main-container
    .title-section.d-flex.h-5.w-100.mb-2.mt-2
      .column1.w-25.mr-2
        Bundesland:
        = select_tag 'state',  options_for_select([ ['Bitte auswählen', 'none'], ['Baden-Württemberg', 'BW'] , ['Bayern', 'BY'] ,['Berlin', 'BE'],['Brandenburg',
        'BB'],['Bremen', 'HB'],['Hamburg', 'HH'],['Hessen', 'HE'],['Mecklenburg-Vorpommern', 'MV'],['Niedersachsen', 'NI'],['Nordrhein-Westfalen', 'NW'],['Rheinland-Pfalz', 'RP'],
        ['Saarland', 'SL'],['Sachsen', 'SN'],['Sachsen-Anhalt', 'ST'],['Schleswig-Holstein', 'SH'], ['Thüringen', 'TH']], selected: @which_state), class: 'stateSelector'
      .column3.w-25.d-flex{:style => "margin-left:665px"}
        %p3 Termine planbar?
    .contain-section.d-flex.w-100#div_holiday_head_els
      %form.contain-col1#div_holiday_head
        - holiday_front_end_id = 1
        - unique_id = 0
        - @holidays.each do |single_day|
          - planable = single_day.planable
          .div.holiday_rows.d-flex.w-100{class: (single_day.fest === 1 ? 'fixed_holidays' : 'customized_holidays' ) }
            -#= hidden_field_tag "fixed_holidays[]", single_day.fest, :class => "fixed_holidays_#{single_day.fest == 1}"
            = hidden_field_tag "holiday_db_id[]", single_day.id , :class => "holiday_db_id"
            = hidden_field_tag "holiday_front_end_id[]", holiday_front_end_id , :class => "holiday_front_end_id"
            = text_field_tag "holiday_date[]",  single_day.datum.strftime("%d.%m.%Y"), :class => "mr-5 mb-1 dateField", :style => "width: 222px;",disabled: single_day.fest == 0 ? false : true, :id=> unique_id
            = text_field_tag "holiday_name[]",  single_day.bezeichnung, :class => "mr-5 mb-1 nameField",:style => "width:600px",disabled: single_day.fest == 0 ? false : true , :id => unique_id
            = check_box_tag 'holiday_planable[]', planable, planable, {:class => "holidayCheckbox"}
            .div.ml-4{:style => "display: #{single_day.fest == 0 ? 'show': 'none' } "}
              .btn.btn-danger.d-flex.justify-content-center.align-items-center.delBtn{:role=>"button", :style=> "height:30px; width:30px;"}
                %i.fa.fa-trash



            - holiday_front_end_id += 1
            - unique_id += 1
        = hidden_field_tag :holiday_front_end_id_global, holiday_front_end_id
        = hidden_field_tag :firma, @branch.hdl_firma, class:"hdl_firma"
        = hidden_field_tag :filiale, @branch.hdl_filiale, class:"hdl_filiale"
    .add-btn-section.d-flex.justify-content-center.h-5.w-100.mt-1
      .div.btn-add-new-row.d-inline.border.text-decoration-none.text-light.border-0.text-align-cente#btnAdd
        %span.fa.fa-plus-circle
    .save-btn-section.d-flex.justify-content-center.h-5.w-100.mt-1
      .button-1-wtp.button-padding-1-wtp#btnSubmit{:style => "cursor: pointer; font-weight: bold;"} Speichern




.div_holiday_row_template.d-none
  .new_row.d-flex.w-100
    -#= hidden_field_tag "fixed_holidays[]", 1, class: "fixed_holidays"
    = hidden_field_tag "holiday_db_id[]", 0, class: "holiday_db_id"
    = hidden_field_tag "holiday_front_end_id[]", 0, class: "holiday_front_end_id"
    = text_field_tag 'holiday_date[]',  '', :class => "mr-5 mb-1 newDateField", :style => "width: 222px;"

    = text_field_tag 'holiday_name[]', '', :class => "mr-5 mb-1 newNameField", :style => "width:600px"

    = check_box_tag 'holiday_planable[]', '', false, {:class => "holidayCheckboxTemplate", :type=>"checkbox"}

    .btn.btn-danger.ml-4.d-flex.justify-content-center.align-items-center.delBtn{:role=>"button", :style=> "height:30px; width:30px;"}
      %i.fa.fa-trash
````

<sup align="right"><a href="#table-of-contents">Go to top</a></sup>

## Form validation using JavaScript

````javascript

$(".holidays.index").ready(function () {
    //when pages loaded then move customized holiday after the fixed holidays
    $(".customized_holidays").insertAfter($("#div_holiday_head > div.fixed_holidays").last()).next();

    //an empty array to change planable status for state holiday
    var state_holiday_planable = [];
    //initibalize an empty array to save holidays when state changed
    var state_changed_records = [] ;
    //initialize an empty array to create new holiday
    var changed_records = [];
    //initialize an empty array to update customized holiday
    var created_records = [];
    //select hdl firma
    var hdl_firma = $(".hdl_firma").val();
    //select hdl filiale
    var hdl_filiale = $(".hdl_filiale").val();
    // Date picker function to pick date for date Field
    function runDatePicker() {
        $('.dateField').datepicker({
            // pick the date on select
            onSelect: function () {
                //go to the parent element when checkbox is checked
                var header_element = $(this).closest('.holiday_rows');
                //run the function to create or update records
                created_changed_records(header_element);
            }
        });
    }
    //run the date picker function
    runDatePicker();
    // change the text in LC Switch
    function change_checkbox_text(){
        $('.holidayCheckbox').off().lc_switch('Ja', 'Nein');
    }
    //run the lc switch checkbox text changer
    change_checkbox_text();


    //checkbox trigger function
    function chaeckboxTriggerFunction() {
        $('body').delegate('.holidayCheckbox', 'lcs-statuschange', function () {
            //go to the parent element when checkbox is triggered
            var header_element = $(this).closest('.holiday_rows');

            //run the function to create or update records as an array
            created_changed_records(header_element);
        });
    }
    //run the checkbox trigger function
    chaeckboxTriggerFunction();

    //Declare a variable to crate a dynamic frontend id for new row
    var holiday_front_end_id_global = 0;
    //change the holidays according to state
    $(".stateSelector").change(function () {
        //collect the state value
        var state_value = $(".stateSelector").val();
        //Declare new form to send an ajax request to filler holiday
        var form_data = new FormData();
        form_data.append('state', JSON.stringify(state_value));
        $.ajax({
            // send to the get_holiday controller
            url: "get_holidays",
            //request type
            type: "POST",
            //form data
            data: form_data,
            //This request expecting a response from server side
            async: false,
            //contentType option to false is used for multipart/form-data forms that pass files
            contentType: false,
            //processData is false jQuery simply sends whatever I specify as data in an Ajax request without any attempt to modify it by encoding as a query string.
            processData: false,
            //response handling
            success: function (data) {
                created_records = [];
                changed_records = [];
                var response_holiday = data;
                //if received a feedback from server sice (controller)
                if (response_holiday.length > 0) {
                    //sort those row according to date
                    var _data = response_holiday.sort((a, b) => (a.date > b.date) ? 1 : -1);
                    //remove all fixed holidays
                    $(".fixed_holidays").remove();
                    //remove also the old state holiday to show new state holidays
                    $(".holiday_rows").find($(".new_row")).remove();
                    //Loop the all responded data and replace inside the holiday day element
                    for (var i = 0; i < _data.length; i++) {
                        //clone new row and exchange their class name
                        var new_record = $('.div_holiday_row_template').clone(true).removeClass('div_holiday_row_template').addClass('holiday_rows');
                        new_record.find('.holidayCheckboxTemplate').removeClass('holidayCheckboxTemplate').addClass('holidayCheckbox');
                        new_record.find('.newDateField').removeClass('newDateField').addClass('dateField');
                        new_record.find('.newNameField').removeClass('newNameField').addClass('nameField');
                        //collect the holiday name from the responded data
                        var name = _data[i].name;
                        //collect the holiday date from the responded data
                        var _date = _data[i].date;
                        //change the Date format
                        var formatedDate = moment(_date).format('DD.MM.YYYY');
                        //replace the name attribute for date field
                        new_record.find(".dateField").attr('name', name);
                        //replace the value for date field and active as a disabled element
                        new_record.find(".dateField").val(formatedDate).prop('disabled', true);
                        //replace the name attribute for name field
                        new_record.find(".nameField").val(name);
                        //replace the value for name field and active as a disabled element
                        new_record.find(".nameField").attr('name', name).prop('disabled', true);
                        //as a fixed holiday we dont need to the delete button
                        new_record.find(".delBtn").remove();
                        //show to new added row
                        new_record.removeClass('d-none');
                        //append the new added row inside the parent element
                        $("#div_holiday_head").append((new_record));

                        //run the lc switch text change function
                        change_checkbox_text();


                        // created_changed_records(new_record);
                        state_changed_records.push({
                            internal_id: i,
                            holiday_date: _date,
                            holiday_name: name,
                        });
                        //scroll to bottom when added a new row
                        scroll_to_bottom_of('#div_holiday_head_els');
                    }
                    //sort customized_holidays
                    $(".customized_holidays").insertAfter($(".holiday_rows > div.new_row").last()).next();
                }
            },
            //error handling
            error: function () {
            }

        });
    });

    holiday_front_end_id_global = $('#holiday_front_end_id_global').val();

    //add new row when click the add button
    $("#btnAdd").click(function () {
        //clone the new row template
        var newField = $(".div_holiday_row_template").clone().removeClass("div_holiday_row_template").addClass("holiday_rows");
        newField.find('.newDateField').removeClass('newDateField').addClass('dateField');
        newField.find('.newNameField').removeClass('newNameField').addClass('nameField');
        newField.find(".holidayCheckboxTemplate").removeClass("holidayCheckboxTemplate").addClass("holidayCheckbox");
        //set a dynamic id for every new added rows (without new id , datepicker doesnt work)
        newField.find('.dateField').attr("id", "newIdName" + holiday_front_end_id_global);
        newField.removeClass('d-none');
        newField.find('.holiday_front_end_id').val(holiday_front_end_id_global);
        //append the template inside the parent div
        $("#div_holiday_head").append(newField);
        //scroll to bottom when added a new row
        scroll_to_bottom_of('#div_holiday_head_els');
        //run the date picker
        runDatePicker();
        //run the lc switch text change function
        change_checkbox_text();

        keyup_trigger_function_for_nameField();

        //run the delete tripper function to delete unsaved holiday
        holidays_delete_button_trigger();

        holiday_front_end_id_global = parseInt(holiday_front_end_id_global) + 1
    });

    function keyup_trigger_function_for_nameField() {
        $(".nameField").keyup(function () {
            //Select the parent element once typed someting in name input field
            var header_element = $(this).closest(".holiday_rows");
            //Run the delete function with passing header element as parameter
            created_changed_records(header_element);
        });
    }
    //run keyup trigger function when namefield triggered
    keyup_trigger_function_for_nameField();

    //delete button trigger function
    function holidays_delete_button_trigger() {
        $(".delBtn").click(function () {
            //Select the parent element once delete button
            var head_element = $(this).closest('.holiday_rows');
            //Run the delete function with passing header element as parameter
            delete_holiday(head_element);
        });
    }
    //run the delete tripper function to delete saved holiday
    holidays_delete_button_trigger();

    //delete function to delete holiday row and save the update value in the database
    function delete_holiday(head_element){
        //Select the db id
        var db_id = head_element.find('.holiday_db_id').val();
        //if db id exist then send a XMLHttpRequest to controller to delete that row
        if (db_id !== "0") {
            //declare a delete form
            var deletedData = new FormData();
            //and store into it db_id of that row
            deletedData.append("db_id", db_id);
            if (db_id !== "0"){
                $.ajax({
                    url: 'destroy',
                    type: 'DELETE',
                    data: deletedData,
                    //This request expecting a response from server side
                    async: false,
                    //contentType option to false is used for multipart/form-data forms that pass files
                    contentType: false,
                    //processData is false jQuery simply sends whatever I specify as data in an Ajax request without any attempt to modify it by encoding as a query string.
                    processData: false,
                    //response handling
                    success: function () {
                        //if deleted successfully sent a notify to the browser
                        send_to_parent(delete_notice());
                    },
                    //error handling
                    error: function () {
                        send_to_parent(error_notice());
                    }
                })
            }
            //Remove all element from that parent element
            head_element.remove();
        }else {
            //Remove all element from that parent element
            head_element.remove();
        }
    }
    //function to create or update an object
    function created_changed_records(header_element){
        //select the db id of that row
        var db_id = header_element.find(".holiday_db_id").val();
        db_id = parseInt(db_id);
        //select the internal id of that row
        var internal_id = header_element.find(".holiday_front_end_id").val();
        internal_id = parseInt(internal_id);
        console.log("Checkbox triggered : internal id "+ internal_id + " db id " + db_id );
        //select new holiday date
        var newDate = header_element.find(".dateField").val();
        //select holiday name
        var name = header_element.find(".nameField").val();
        //check checkbox status
        var checkbox_element = header_element.find(".holidayCheckbox");
        //declare initial value as a checkbox status
        var _status = 0;
        //if checked the checkbox then interchange the status
        if (checkbox_element.is(":checked")) {
            _status = 1;
        } else {
            _status = 0;
        }
        //check the holiday is already exist in database or not
        if (db_id === 0 && internal_id > 0) {
            //check internal id exist or not
            record_exists = created_records.find(element => element['internal_id'] === internal_id);
            //if internal id exist then push all value inside created_record array
            if (record_exists !== undefined) {
                const index = created_records.findIndex(prop => prop['internal_id'] === internal_id);
                created_records.splice(index, 1);
                created_records.push({
                    internal_id: internal_id,
                    db_id: db_id,
                    holiday_date: newDate,
                    holiday_name: name,
                    planable: _status
                });

            } else {
                created_records.push({
                    internal_id: internal_id,
                    db_id: db_id,
                    holiday_date: newDate,
                    holiday_name: name,
                    planable: _status
                });
            }
            // if holiday dose not exist in database then create a new array to create a new holiday
        }else if (internal_id > 0 && db_id > 0) {
            //check internal id exist or not
            record_exists = changed_records.find(element => element['db_id'] === db_id);
            //if internal id exist then push all value inside created_record array
            if (record_exists !== undefined){
                const index = changed_records.findIndex(prop => prop['db_id'] === db_id);
                changed_records.splice(index, 1);
                changed_records.push({
                    internal_id: internal_id,
                    db_id: db_id,
                    holiday_date: newDate,
                    holiday_name: name,
                    planable: _status

                });
            }else {
                changed_records.push({
                    internal_id: internal_id,
                    db_id: db_id,
                    holiday_date: newDate,
                    holiday_name: name,
                    planable: _status
                });
            }
        } else {
            state_holiday_planable.push({
                planable_status: _status,
                state_holiday_date: newDate,
                state_holiday_name: name
            });


        }
    }




    $("#btnSubmit").click(function () {
        //Declare a new array to save state value
        var state_update = {
            state_value : $(".stateSelector").val(),
            hdl_firma   : $(".hdl_firma").val(),
            hdl_filiale : $(".hdl_filiale").val()
        };
        //Declare a new formData and append into it all array
        var newDataForm = new FormData();
        //appends a new value onto an existing key inside a FormData object
        //JSON.stringify() method converts a JavaScript object or value to a JSON string
        newDataForm.append("state_updated",             JSON.stringify(state_update));
        newDataForm.append('created_records',           JSON.stringify(created_records));
        newDataForm.append('changed_records',           JSON.stringify(changed_records));
        newDataForm.append("state_changed_records",     JSON.stringify(state_changed_records));
        newDataForm.append("state_planable_records",     JSON.stringify(state_holiday_planable));
        console.log("all state holidays : " + JSON.stringify(state_changed_records));


        //send the ajax request to the controller
        $.ajax({
            url: 'update',
            type: 'PATCH',
            data: newDataForm,
            //This request expecting a response from server side
            async: false,
            //contentType option to false is used for multipart/form-data forms that pass files
            contentType: false,
            //processData is false jQuery simply sends whatever I specify as data in an Ajax request without any attempt to modify it by encoding as a query string.
            processData: false,

            //Response handling
            success: function (data) {
                //if response the ajax request
                var response = data;
                //loop the response data and change the value of holiday_front_end_id to db id
                if (response.length > 0) {
                    for(var i= 0; i < response.length; i++) {
                        $("input[name='holiday_front_end_id[]'][value='" + response[i].internal_id + "']").parent().find("input[name='holiday_db_id[]']").val(response[i].db_id);
                    }
                }
                send_to_parent(success_notice());
                //set zero value to the changed records object
                changed_records = [];
                //set zero value to the created records object
                created_records = [];
                //reset state changed records array to save new records
                state_changed_records = [];
                //reset state holiday planable array to store new value
                state_holiday_planable = [];

            },
            //error handling
            error: function (data) {
                send_to_parent(error_notice());
            }

        })
    });
});
````
<sup align="right"><a href="#table-of-contents">Go to top</a></sup>

## Backend Handling with Ruby

Mapping: config/routes.rb

````ruby
  get 'holidays/:branch_id' => 'holidays#index'
  resources :holidays, only: [:index, :update, :destroy]
  post 'holidays/get_holidays' => 'holidays#get_holidays'
````

Controller: app/controllers/holidays_controller.rb

````ruby
class HolidaysHelperModel < ActiveRecord::Base
  self.table_name = 'can_feiertage_helper'
end

class Setting < ActiveRecord::Base
  self.table_name = 'can_wiedenn'
  self.primary_key = 'wie_id'
end

class HolidaysController < ApplicationPureBootstrapController
  include HolidaysHelper
  require 'settings_helper'
  require 'json'

  def get_holidays
    #receive the state value
    state = JSON.parse(params[:state])
    #select the current year
    year = Time.current.year

    holidays = []
    #filter the holiday according to state and current year
    holidays.concat get_holidays_for_state(state, year)
    holidays.concat get_holidays_for_country(year)
    #increment current year
    year = year + 1
    #filter the holiday and collect them according to state and next year
    holidays.concat get_holidays_for_state(state, year)
    holidays.concat get_holidays_for_country(year)
    #send back the filtered holidays as a response
    render json: holidays
  end

  def index
    #declare an instance variable to find a active branch
    @branch = Branch.find_by(hdl_id: params[:branch_id])
    #select which state is active
    @which_state = get_setting_string('Bundesland für Feiertag', @branch.hdl_firma, @branch.hdl_filiale)
    #select all holiday according to firma and filiale
    @holidays = HolidaysHelperModel.where(firma: @branch.hdl_firma, filiale: @branch.hdl_filiale).order("datum ASC")
  end

  def update
    # the result array that would be send back as response
    result = []
    # array to log if an error happened
    error_occurred = []

    # received state value to update state status
    state_update = JSON.parse(params[:state_updated])
    # received values to update existing holiday
    changed_records_array = JSON.parse(params[:changed_records])
    # received values to create new holiday
    created_records_array = JSON.parse(params[:created_records])
    # received state holidays when state changed
    state_updated_records = JSON.parse(params[:state_changed_records])
    # checkbox value when state would be changed
    state_planable_records_array = JSON.parse(params[:state_planable_records])

    begin
      #if state changed then update the selected state value in can_wiedenn
      if state_update.present?
        #update state by changing state selector
        state_updated = Setting.find_by(wie_firma: state_update['hdl_firma'].to_s.strip.to_i , wie_filiale: state_update['hdl_filiale'].to_s.strip.to_i, wie_module: 'Bundesland für Feiertag').update(wie_todo: state_update['state_value'])
        unless state_updated
          #if state not saved than push 1 as an error
          error_occurred.push(1)
        end
      end
    rescue Exception => e
      logger.info "error to save holiday: #{e.to_s}"
      render status: 404 , json: {code: 404, message: 'Method Not Found'}
    end



    begin
      # if state changed then save all holidays of that state
      if state_updated_records.length > 0
        # delete all the previous existing fixed holidays
        HolidaysHelperModel.where(firma: state_update['hdl_firma'].to_s.strip.to_i, filiale: state_update['hdl_filiale'].to_s.strip.to_i, fest: 1).delete_all
        state_updated_records.each do |single_element|
          # create new holidays record according to state (insert into table)
          create_state_holiday = HolidaysHelperModel.create(datum: single_element['holiday_date'].to_s.strip.to_date.strftime("%Y-%m-%d"), bezeichnung: single_element['holiday_name'], firma: state_update['hdl_firma'].to_s.strip.to_i, filiale: state_update['hdl_filiale'].to_s.strip.to_i, fest: 1)

          unless create_state_holiday
            # if holidays do not created  successfully
            # push 1 as error message
            error_occurred.push(1)
          end
        end
      end
      rescue Exception => e
        logger.info "error to save holiday: #{e.to_s}"
        render status: 404 , json: {code: 404, message: 'Method Not Found'}
    end

    begin
      # update planable status when changed the state
      if state_planable_records_array.length > 0
        state_planable_records_array.each do |single_element|
          # update the checkbox status to the database according to holiday date and name of that checkbox
          state_planable_updated = HolidaysHelperModel.find_by(firma: state_update['hdl_firma'].to_s.strip.to_i, filiale: state_update['hdl_filiale'].to_s.strip.to_i, fest: 1, datum: single_element['state_holiday_date'].to_s.strip.to_date.strftime("%Y-%m-%d"), bezeichnung:single_element['state_holiday_name']).update(planable:single_element['planable_status'].to_i)
          unless state_planable_updated
            error_occurred.push(1)
          end
        end
      end
    rescue Exception => e
      logger.info "error to save holiday: #{e.to_s}"
      render status: 404 , json: {code: 404, message: 'Method Not Found'}
    end

    begin
      # if the array has something we would loop it
    if created_records_array.length > 0
        # loop array
        created_records_array.each do |single_element|
          # db_id
          db_id = single_element['db_id'].to_i
          # to make sure that it is a new record
          if db_id === 0
            # create record (insert into table)
            create_record = HolidaysHelperModel.create(datum: single_element['holiday_date'].to_s.strip.to_date.strftime("%Y-%m-%d"), bezeichnung: single_element['holiday_name'], planable: single_element['planable'].to_i, firma: state_update['hdl_firma'].to_s.strip.to_i, filiale: state_update['hdl_filiale'].to_s.strip.to_i)
            # if create was successful
            if create_record
              # push the internal id and db id to the result array
              puts "db_id: #{create_record['id']}  internal_id: #{single_element['internal_id']}"
              result.push({db_id: create_record['id'], internal_id: single_element['internal_id']})
            else
              # in case of an error add to error array
              error_occurred.push(1)
            end
          end
        end
      end
    rescue Exception => e
      logger.info "error to save holiday: #{e.to_s}"
      render status: 404 , json: {code: 404, message: 'Method Not Found'}
    end

    begin
      if changed_records_array.length > 0
        #update the customized holidays if changed anyting
        changed_records_array.each do |single_element|
          update_record = HolidaysHelperModel.find_by(id: single_element['db_id']).update(datum: single_element['holiday_date'].to_s.strip.to_date.strftime("%Y-%m-%d"), bezeichnung: single_element['holiday_name'], planable: single_element['planable'])
          unless update_record
            error_occurred.push(1)
          end
        end
      end
    rescue Exception => e
      logger.info "error deleting holiday: #{e.to_s}"
      render status: 304 , json: {code: 304, message: 'Method Not Modified'}
    end
    if error_occurred.length > 0
      head :not_found
    else
      render json: result
    end
  end


  def destroy
    begin
      #check db_id exist or not
      if params[:db_id].present?
        # if exist then find it and delete it
        delete_records = HolidaysHelperModel.find_by(id: params[:db_id]).delete
        #if successfully deleted
        if delete_records
          # show a success notification
          head :ok
          #if successfully not deleted
        else
          # show an error notification
          head :not_found
        end
       #if  db_id not exist then show an error notification on browser
      else
        head :not_found
      end
      #if db_id not found
    rescue Exception => e
      # send an error message in console
      logger.info "error deleting holiday: #{e.to_s}"
      # and also send an error message to the browser
      render status: 400 , json: {code: 400, message: 'Method Not Allowed'}
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
