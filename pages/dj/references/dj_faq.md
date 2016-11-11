---
title: FAQ layout
permalink: dj_faq.html
sidebar: dj_sidebar
tags: [special_layouts]
keywords: frequently asked questions, FAQ, question and answer
last_updated: November 30, 2015
summary: "You can use an accordion-layout that takes advantage of Bootstrap styling. This is useful for an FAQ page."
toc: false
folder: dj/references
---

<div class="panel-group" id="accordion">
  <div class="panel panel-default">
      <div class="panel-heading">
          <h4 class="panel-title">
              <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseOne">Is DataJoint ready?</a>
          </h4>
      </div>
      <div id="collapseOne" class="panel-collapse collapse noCrossRef">
          <div class="panel-body">
          The MATLAB and Python versions of DataJoint are mature. Any outstanding issues can be viewed or submitted here: https://github.com/datajoint/datajoint-matlab/issues.
          </div>
      </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
      <div class="panel-heading">
          <h4 class="panel-title">
              <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse2">What kind of data can DataJoint accommodate?</a>
          </h4>
      </div>
      <div id="collapse2" class="panel-collapse collapse noCrossRef">
          <div class="panel-body">
          DataJoint is a framework that helps organize data using the relational data model. It can accommodate any data. In its most common use, data are inserted from MATLAB, so if it can be stored in a MATLAB variable, it can be stored in DataJoint.
          </div>
      </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
      <div class="panel-heading">
          <h4 class="panel-title">
              <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse3">How big are the datasets that can be handled with DataJoint?</a>
          </h4>
      </div>
      <div id="collapse3" class="panel-collapse collapse noCrossRef">
          <div class="panel-body">
          DataJoint uses MySQL's InnoDB engine as its back end. The data size limits can be found in the [MySQL documentation](http://dev.mysql.com/doc/refman/5.6/en/limits.html). For example, the maximum table space managed by the InnoDB engine (DataJoint's default) is 64 TiB, but the data are typically distributed across multiple tables.
          </br>
          The daily output of our lab may be on the order of hundreds of GB of raw data. The processing chain extracts only the essential data and imports it into the database. Still, even the essential data may exceed tens of GB per day.

          </div>
      </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
      <div class="panel-heading">
          <h4 class="panel-title">
              <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse4">Do you store raw data in the database?</a>
          </h4>
      </div>
      <div id="collapse4" class="panel-collapse collapse noCrossRef">
          <div class="panel-body">
          Typically not. The acquisition software writes out the raw data into files. The DataJoint processing chain then imports the essential data from raw files. For example, the class [`aod.TraceSet`](https://github.com/atlab/sessions/blob/master/schemas/%2Baod/TraceSet.m) extracts the fluorescence traces for a set of cells in an AOD-based two-photon scan. The raw scan data is read from an external file and only the significantly smaller result of the computation is stored in the database (See https://github.com/atlab/sessions).
          </br>
          However, it's up to the user to decide which data go into the database. Since DataJoint uses regular MySQL tables to store data, other software can be written to write data directly into the database bypassing DataJoint altogether.

          </div>
      </div>
  </div>
  <!-- /.panel -->
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse5">What alternative systems did you consider and why did you end up writing your own system?</a>
            </h4>
        </div>
        <div id="collapse5" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
            In many labs, the default data management scheme comprises the OS file system with clever naming conventions with additional meta data stored in CSV, XML, .MAT files, for example. This works fine for dead storage, but makes more interesting queries difficult.  Also, data dependencies are difficult to maintain.
            </br>
            Another alternative is to use a relational database system (e.g. MySQL) using SQL. However, SQL queries are typically cumbersome. SQL requires significant training to be used effectively. DataJoint makes using the proper relational data model easier. More on this in [[how is DataJoint different?]]
            </br>
            DataJoint met our need for a flexible, light-weight, yet capable system that is easy to learn and use.

            </div>
        </div>
    </div>
    <!-- /.panel -->



</div>
<!-- /.panel-group -->

{% include links.html %}
