## Dialog Acceptance Management
![image](https://user-images.githubusercontent.com/77357735/183630682-b29d1a14-15ff-4140-b176-66449d9d9244.png)

## Table of Contents

- [Project Description](#project-description)
- [Technologies and Programing Languages](#technologies-and-programming-languages)
- [Hello world](#hello_world)
- Frontend Design
  - [Modal Dialog from php](#modal_dialog_from_php)
  - [Hello world](#hello_world)
  - [Frontend Design with HAML and Bootstrap](#frontend_design_with_haml_and_bootstrap)
  
  - [Frontend Design HTML](#frontend-design-html)
  - [Frontend Design CSS](#frontend-design-css)
  - [Frontend Design JavaScript](#frontend-design-javascript)
- Backend
  - [Backend Handling with Ruby](#backend-handling-with-ruby)
- [Contact](#contact)


## Project Description

## Technologies and Programming Languages

Frontend: [HAML](https://haml.info/), [Bootstrap](https://getbootstrap.com/)

Backend: [Ruby](https://www.ruby-lang.org/de/), [PHP](https://www.php.net)

Framework: [Ruby on Rails](https://rubyonrails.org/)


## Modal Dialog from php

## Hello World

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



## Contact

Rahman Habib - [habib2030@web.de](mailto:habib2030@web.de)

LinkedIn Profile: [smhabibjr](https://www.linkedin.com/in/smhabibjr)

Youtube Channel: [HabibJr](https://www.youtube.com/c/HabibJr)

Youtube Channel: [HabibJr](https://www.facebook.com/smhabibjr)
