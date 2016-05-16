
Ajax custom search and update form PLUGIN code--->
____________________________________________________



add_shortcode( 'myform', 'bartag_func' );

add_action('admin_menu', 'my_plugin_menu');

function my_plugin_menu() {
  add_menu_page('My Options', 'My Form', 'manage_options', 'my-plugin.php', 'my_plugin_page');
  add_submenu_page( 'my-plugin.php', 'My Custom Form List', 'List', 'manage_options', 'my-custom-form-list', 'my_custom_form_list_callback' );
  add_submenu_page( 'my-plugin.php', 'My Custom Form Search', 'Search', 'manage_options', 'my-custom-form-search', 'my_custom_form_search_callback' );
  
}
function my_custom_form_list_callback(){
  global $wpdb;
  $table = $wpdb->prefix . 'myplugin';
  $results = $wpdb->get_results( "SELECT * FROM $table" );
  //echo "<pre>";
  //print_r($result);
  $i=1;
  ?>
  <script type="text/javascript" >
  jQuery( document ).ready(function() {
  // Handler for .ready() called.
  jQuery( ".deleteData" ).click(function() {
      var d = jQuery(this).attr("murlee");
      //alert(d);
      jQuery.ajax({
        url: '<?php echo admin_url('admin-ajax.php'); ?>',
        data: {
            'action':'example_ajax_request',
            'id' : d
        },
        success:function(data) {
            // This outputs the result of the ajax request
            console.log(data);
            //alert(data);
            if(data=="success")
            {
              jQuery("#row"+d).fadeOut();
            }
        },
        error: function(errorThrown){
            console.log(errorThrown);
        }
       });   
    });
  jQuery( ".editData" ).click(function() {
      jQuery(this).hide();
      var d = jQuery(this).attr("murlee");
      //alert("#row"+d+" kk");
      jQuery(".hidden"+d).show();
      jQuery("#row"+d+" td label").hide();
      jQuery(".updateData"+d).show();
       
    });
  jQuery(".updateData").click(function(){
    jQuery(this).hide();
    var d = jQuery(this).attr("murlee");
    var name = jQuery(".name"+d).val();
    var lastname = jQuery(".lastname"+d).val();
    var email = jQuery(".email"+d).val();
    var gender = jQuery(".gender"+d).val();
    var course = jQuery(".course"+d).val();
    //alert(name);
    jQuery(".hidden"+d).hide();
    jQuery("#row"+d+" td label").show();
    jQuery(".updateData"+d).hide();
    jQuery(".editData"+d).show();
     jQuery.ajax({
        url: '<?php echo admin_url('admin-ajax.php'); ?>',
        data: {
            'action':'update_ajax_request',
            'id' : d,
            'name' : name,
            'lastname' : lastname,
            'email' : email,
            'gender' : gender,
            'course' : course
        },
        success:function(data) {
            // This outputs the result of the ajax request
            console.log(data);
            //alert(data);
            jQuery("#row"+d+" td .name").html(name);
            jQuery("#row"+d+" td .lastname").html(lastname);
            jQuery("#row"+d+" td .email").html(email);
            jQuery("#row"+d+" td .gender").html(gender);
            jQuery("#row"+d+" td .course").html(course);
        },
        error: function(errorThrown){
            console.log(errorThrown);
        }
       });
  });
});
  </script>

<table border="1" style="margin-top: 12px;">
<thead>
  <tr>
    <th>id</th>
    <th>name</th>
    <th>lastname</th>
    <th>email</th>
    <th>gender</th>
    <th>course</th>
    <th>Delete</th>
    <th>Edit</th>
  </tr>
</thead>
<?php foreach ($results as $result) { ?>
  <tr id="row<?php echo $result->id; ?>">
    <td><?php echo $i; ?></td>
    <td><label class="name"><?php echo $result->name; ?></label><input class="hidden<?php echo $result->id; ?> name<?php echo $result->id; ?>" type="text" name="name" value="<?php echo $result->name; ?>" style="display: none;"></td>
    <td><label class="lastname"><?php echo $result->lastname; ?></label><input class="hidden<?php echo $result->id; ?> lastname<?php echo $result->id; ?>" type="text" name="lastname" value="<?php echo $result->lastname; ?>" style="display: none;"></td>
    <td><label class="email"><?php echo $result->email; ?></label><input class="hidden<?php echo $result->id; ?> email<?php echo $result->id; ?>" type="text" name="email" value="<?php echo $result->email; ?>" style="display: none;"></td>
    <td><label class="gender"><?php echo $result->gender; ?></label><input class="hidden<?php echo $result->id; ?> gender<?php echo $result->id; ?>" type="text" name="gender" value="<?php echo $result->gender; ?>" style="display: none;"></td>
    <td><label class="course"><?php echo $result->course; ?></label><input class="hidden<?php echo $result->id; ?> course<?php echo $result->id; ?>" type="text" name="course" value="<?php echo $result->course; ?>" style="display: none;"></td>
    <td><input class="deleteData" type="button" name="delete" murlee="<?php echo $result->id; ?>" value="Delete" /></td>
    <td><input class="editData editData<?php echo $result->id; ?>" type="button" name="edit" murlee="<?php echo $result->id; ?>" value="Edit" /><input id="" class="updateData updateData<?php echo $result->id; ?>" type="button" name="update" murlee="<?php echo $result->id; ?>" value="Update" style="display: none;"></td>
  </tr>
<?php $i++; } ?>

</table>
  <?php
}
function my_plugin_page()
{

if(isset($_POST['submit']))
{
   $chek = $_POST['check'];
   //print_r($chek);
   update_option( 'setting_form', $chek ); 
   //var_dump(get_option("setting_form")); 
}
?>
  
<form method="post">
First Name:<input type="checkbox" name="check[]" <?php if(in_array("fname", get_option("setting_form"))) echo "checked"; ?> value="fname" />
Last Name:<input type="checkbox" name="check[]" <?php if(in_array("lname", get_option("setting_form"))) echo "checked"; ?> value="lname" />
Email:<input type="checkbox" name="check[]" <?php if(in_array("email", get_option("setting_form"))) echo "checked"; ?> value="email" />
Gender:<input type="checkbox" name="check[]" <?php if(in_array("gender", get_option("setting_form"))) echo "checked"; ?> value="gender" />
Course:<input type="checkbox" name="check[]" <?php if(in_array("course", get_option("setting_form"))) echo "checked"; ?> value="course" />
<input type="submit" name="submit" value="submit" />
</form>
  <?php
}
function example_ajax_request() {
 
    // The $_REQUEST contains all the data sent via ajax
    if ( isset($_REQUEST) ) {
     
        $id = $_REQUEST['id'];
        global $wpdb;
        $table = $wpdb->prefix . 'myplugin';
        $delete = $wpdb->query(" DELETE FROM $table where id = '$id'");
        
        if($delete)
        {
          echo "success";
        }
        else{
          echo "fail";
        }
     
    }
     
    // Always die in functions echoing ajax content
   die();
}
 
add_action( 'wp_ajax_example_ajax_request', 'example_ajax_request' );

/* this is search section */
add_action( 'wp_ajax_search_ajax_request', 'search_ajax_request' );
function my_custom_form_search_callback(){

  ?>
  <script type="text/javascript">
  jQuery(document).ready(function(){
    jQuery("form").submit(function(){
      var nm = jQuery("#search").val();
      jQuery.ajax({
        url: '<?php echo admin_url('admin-ajax.php'); ?>',
        data: {
            'action':'search_ajax_request',
            'name' : nm
        },
        success:function(data) {
             //alert(data);
             jQuery(".mysearch").html(data);
             jQuery( ".deleteData" ).click(function() {
      //alert('hello');
      var d = jQuery(this).attr("murlee");
      //alert(d);
      jQuery.ajax({
        url: '<?php echo admin_url('admin-ajax.php'); ?>',
        data: {
            'action':'example_ajax_request',
            'id' : d
        },
        success:function(data) {
            // This outputs the result of the ajax request
            console.log(data);
            //alert(data);
            if(data=="success")
            {
              jQuery("#row"+d).fadeOut();
            }
        },
        error: function(errorThrown){
            console.log(errorThrown);
        }
       });   
    });
        },
        error: function(errorThrown){
            console.log(errorThrown);
        }
       });
    });

  
  });
  </script>
  <h1>Search Name From Here</h1>
  <form method="post" action="javascript:;">
    <input type="text" name="search" id="search" value="">
    <input type="submit" name="submit" value="search"> 
  </form>  
  <div class="mysearch"></div>
  <?php
}
function search_ajax_request(){
  
  if(isset($_REQUEST)){
    $name=$_REQUEST['name'];
    global $wpdb;
    $table = $wpdb->prefix . 'myplugin';
    $results = $wpdb->get_results( "SELECT * FROM $table where name LIKE '%".$name."%'" );
    //var_dump($results);
    $i=1;
    ?>
    <table border="1px">
      <tr>
        <th>Sr. No.</th>
        <th>Name</th>
        <th>Last Name</th>
        <th>Email</th>
        <th>Gender</th>
        <th>Course</th>
        <th>Delete</th>
      </tr>
      <?php foreach ($results as $result) { ?>
        <tr id="row<?php echo $result->id; ?>">
          <td><?php echo $i; ?></td>
          <td><?php echo $result->name; ?></td>
          <td><?php echo $result->lastname; ?></td>
          <td><?php echo $result->email; ?></td>
          <td><?php echo $result->gender; ?></td>
          <td><?php echo $result->course; ?></td>
          <td><input class="deleteData" type="button" name="delete" murlee="<?php echo $result->id; ?>" value="Delete" /></td>
        </tr>
     <?php $i++; } ?>
    </table>
    <?php
  }
  
  die();
}
/* this is update section */
add_action( 'wp_ajax_update_ajax_request', 'update_ajax_request' );
function update_ajax_request(){
  if(isset($_REQUEST)){
    $id = $_REQUEST['id'];
    $name = $_REQUEST['name'];
    $lastname = $_REQUEST['lastname'];
    $email = $_REQUEST['email'];
    $gender = $_REQUEST['gender'];
    $course = $_REQUEST['course'];
    //echo $id , ' ', $name ,' ' , $lastname ,' ', $email ,' ', $gender , ' ', $course;
    global $wpdb;
    $table = $wpdb->prefix . 'myplugin';
    $update = $wpdb->query(" UPDATE $table SET
    name='".$_REQUEST['name']."',
    lastname='".$_REQUEST['lastname']."',
    email='$email',
    gender='$gender',
    course='".$_REQUEST['course']."'
    WHERE id = '$id'
    ");
    if($update)
    {
      echo "success";
    }
 }
  die();
}
?>









 
