"# TRS" 

app.py
from flask import Flask, render_template, request, redirect, url_for, session
from flask_mysqldb import MySQL
import MySQLdb.cursors
import re
  
  
app = Flask(__name__)
  
  
app.secret_key = 'xyzsdfg'
  
app.config['MYSQL_HOST'] = 'localhost'
app.config['MYSQL_USER'] = 'root'
app.config['MYSQL_PASSWORD'] = ''
app.config['MYSQL_DB'] = 'user-system'
  
mysql = MySQL(app)
  
@app.route('/')
@app.route('/login', methods =['GET', 'POST'])
def login():
    mesage = ''
    if request.method == 'POST' and 'email' in request.form and 'password' in request.form:
        email = request.form['email']
        password = request.form['password']
        cursor = mysql.connection.cursor(MySQLdb.cursors.DictCursor)
        cursor.execute('SELECT * FROM user WHERE email = % s AND password = % s', (email, password, ))
        user = cursor.fetchone()
        if user:
            session['loggedin'] = True
            session['userid'] = user['userid']
            session['name'] = user['name']
            session['email'] = user['email']
            mesage = 'Logged in successfully !'
            return render_template('user.html', mesage = mesage)
        else:
            mesage = 'Please enter correct email / password !'
    return render_template('login.html', mesage = mesage)
  
@app.route('/logout')
def logout():
    session.pop('loggedin', None)
    session.pop('userid', None)
    session.pop('email', None)
    return redirect(url_for('login'))
  
@app.route('/register', methods =['GET', 'POST'])
def register():
    mesage = ''
    if request.method == 'POST' and 'name' in request.form and 'password' in request.form and 'email' in request.form :
        userName = request.form['name']
        password = request.form['password']
        email = request.form['email']
        cursor = mysql.connection.cursor(MySQLdb.cursors.DictCursor)
        cursor.execute('SELECT * FROM user WHERE email = % s', (email, ))
        account = cursor.fetchone()
        if account:
            mesage = 'Account already exists !'
        elif not re.match(r'[^@]+@[^@]+\.[^@]+', email):
            mesage = 'Invalid email address !'
        elif not userName or not password or not email:
            mesage = 'Please fill out the form !'
        else:
            cursor.execute('INSERT INTO user VALUES (NULL, % s, % s, % s)', (userName, email, password, ))
            mysql.connection.commit()
            mesage = 'You have successfully registered !'
    elif request.method == 'POST':
        mesage = 'Please fill out the form !'
    return render_template('register.html', mesage = mesage)
    
if __name__ == "__main__":
    app.run()
    
    
    
    
  user.html
  <html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>User Account</title>
<!--<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/css/bootstrap.min.css">  -->            

	<!-- Mobile Specific Meta -->
	<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
	<!-- Favicon-->
	<link rel="shortcut icon" href="img/fav.png">
	<!-- Author Meta -->
	<meta name="author" content="">
	<!-- Meta Description -->
	<meta name="description" content="">
	<!-- Meta Keyword -->
	<meta name="keywords" content="">
	<!-- meta character set -->
	<meta charset="UTF-8">
	<!-- Site Title -->

	<title>D Tour</title>
	<link rel="icon" href="./img/j.jpg" type="image/jpg">
	<link href="https://fonts.googleapis.com/css?family=Poppins:100,200,400,300,500,600,700" rel="stylesheet"> 
	
		<!--CSS============================================= -->
		<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/linearicons.css') }}">
		<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/font-awesome.min.css') }}">
		<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/bootstrap.css') }}">
		<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/magnific-popup.css') }}">
		<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/nice-select.css') }}">					
		<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/animate.min.css') }}">
		<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/owl.carousel.css') }}">
		<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/main.css') }}">
</head>
<body>
<div class="container">
	

	<!-- Start Header Area -->
	<header class="default-header">
		<nav class="navbar navbar-expand-lg  navbar-light">
			<div class="container">
				  <a class="navbar-brand" href="index.html">
					  <img src="img/logo.png" alt="">
				  </a>
				  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
					<span class="text-white lnr lnr-menu"></span>
				  </button>

				  
				 <div class="container">
				  <div class="row">	
					Logged in : <strong>{{session.name}} | <a href="{{ url_for('logout') }}"> Logout</a>
				  </div>.

				  <div class="row">
					<img src="{{ url_for('static',filename='static\img\guni_pink_logo.png') }}",height = 50 width = 50/>
					</div>
	</div>
										  
			</div>
		</nav>
	</header>
	<!-- End Header Area -->	
		<!-- start banner Area -->
		<section class="banner-area relative" id="home">	
			<div class="overlay overlay-bg"></div>
			<div class="container">
				<div class="row fullscreen d-flex align-items-center justify-content-center">
					<div class="collapse navbar-collapse justify-content-end align-items-center" id="navbarSupportedContent">
						<ul class="navbar-nav">
							<li><a href="Pages-inside/SignUp.html">SignUp</a></li>
							<li><a href="Pages-inside/login.html">LogIn</a></li>
							<li><a href="./Pages-inside/blog.html">Blogs</a></li>
							<li><a href="#portfolio">Places</a></li>
							<li><a href="#service">Services</a></li>
							<li><a href="./Pages-inside/About.html">About Us</a></li>
						</ul>
					</div>
					<div class="banner-content col-lg-10">
						<h5 class="text-uppercase">Be the part of this Wonderful Journey</h5>
						<h1>
							Incredible India!				
						</h1>
						<a href="https://link.springer.com/article/10.1007/s11042-022-12167-w" class="primary-btn text-uppercase">Explore Now</a>
					</div>											
				</div>
			</div>
		</section>
		<!-- End banner Area -->	
		
		<!-- Start portfolio-area Area -->	
		<section class="portfolio-area section-gap" id="portfolio">
		  <div class="container">
				<div class="row d-flex justify-content-center">
					<div class="menu-content col-lg-10">
						<div class="title text-center">
							<h1 class="mb-10">You'll fall in love with India</h1>
							<p>Explore India from Kashmir to Kaniyakumari</p>
						</div>
					</div>
				</div>
			
			<div class="filters">
			  <ul>
				<li class="active" data-filter="*">All</li>
				<li data-filter=".corporate">North</li>
				<li data-filter=".personal">Central</li>
				<li data-filter=".agency">North-Eastern</li>
				<li data-filter=".portal">South</li>
			  </ul>
			</div>
			
			<div class="filters-content">
			  <div class="row grid">
				<div class="single-portfolio col-sm-4 all corporate">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p1.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h4><a href="./Pages-inside/GoldenTemple-Info.html">Golden Temple</a></h4>
					  <div class="cat">Punjab</div>
					</div>
				  </div>
				</div>
				<br>
				<div class="single-portfolio col-sm-4 all personal">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p2.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h4>Gwalior Fort</h4>
					  <div class="cat">Madhya Pradesh</div>
					</div>
				  </div>
				</div>
				<div class="single-portfolio col-sm-4 all agency">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p9.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h5>Twang</h5>
					  <div class="cat">Arunachal Pradesh</div>
					</div>
				  </div>
				</div>
				<div class="single-portfolio col-sm-4 all portal">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p12.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h5>Alleppey</h5>
					  <div class="cat">Kerala</div>
					</div>
				  </div>
				</div>

				<div class="single-portfolio col-sm-4 all corporate">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p5.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h4><a href="file:///C:/D%20tour/Pages-inside/Rajathan.html">Camel Safari</a></h4>
					  <div class="cat">Rajasthan</div>
					</div>
				  </div>
				</div>
				<div class="single-portfolio col-sm-4 all personal">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p6.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h4>Sanchi Stupa</h4>
					  <div class="cat">Madhya Pradesh</div>
					</div>
				  </div>
				</div>
				<div class="single-portfolio col-sm-4 all agency">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p7.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h5>Umngot river</h5>
					  <div class="cat">Meghalaya</div>
					</div>
				  </div>
				</div>
				<div class="single-portfolio col-sm-4 all portal">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p10.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h5>Vivekananda Rock Memorial</h5>
					  <div class="cat">Tamil Nadu</div>
					</div>
				  </div>
				</div>		
				<div class="single-portfolio col-sm-4 all corporate">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p4.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h4>Mehrangarh Fort</h4>
					  <div class="cat">Rajasthan</div>
					</div>
				  </div>
				</div>
				<div class="single-portfolio col-sm-4 all personal">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p3.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h4>Hampi</h4>
					  <div class="cat">Karnataka</div>
					</div>
				  </div>
				</div>
				<div class="single-portfolio col-sm-4 all agency">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p8.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h5>Mon</h5>
					  <div class="cat">Myanmar</div>
					</div>
				  </div>
				</div>
				<div class="single-portfolio col-sm-4 all portal">
				  <div class="item">
					<img src="{{ url_for('static',filename='img/p11.jpg') }}" alt="Work 1">
					<div class="p-inner">
					  <h5>Colva Beach</h5>
					  <div class="cat">Goa</div>
					</div>
				  </div>
				</div>					        		        
				
			  </div>
			</div>
			
		  </div>
		</section>
		<!-- End portfolio-area Area -->	

		
		<!-- Start service Area -->
		<section class="service-area section-gap relative" id="service">
			<div class="overlay overlay-bg"></div>	
			<div class="container">
				<div class="row d-flex justify-content-center">
					<div class="menu-content pb-60 col-lg-10">
						<div class="title text-center">
							<h1 class="mb-10 text-white">Always in our customer Favour</h1>
							<p>Who are always with tight Budget</p>
						</div>
					</div>
				</div>	
			</div>	
		</section>
		<!-- End service Area -->
		
		<!-- Start services Area -->
		<section class="services-area pb-100" >
			<div class="container">
				<div class="row">
					<div class="col-lg-4">
						<div class="single-service">
							<img class="img-fluid" src="{{ url_for('static',filename='img/s1.png') }}" alt="">
							<h4>Ammenties Selection</h4>
							<p>
								It's totaly on you what you want and what you want to let it go on Hotel Selection process.
							</p>
						</div>
					</div>
					<div class="col-lg-4">
						<div class="single-service">
							<img class="img-fluid" src="{{ url_for('static',filename='img/s2.png') }}" alt="">
							<h4>Visa Ready</h4>
							<p>
								Just 32 Hours - What it takes get your Visa Ready and other documentation necessary for travel.
							</p>
						</div>
					</div>
					<div class="col-lg-4">
						<div class="single-service">
							<img class="img-fluid" src="{{ url_for('static',filename='img/s3.png') }}" alt="">
							<h4>Personalized Travel Plans</h4>
							<p>
								We have wide Varieties of Hotel &amp; Resorts to accomodate you. 3D4N or 15D16N totally upto you.
							</p>
						</div>
					</div>														
				</div>
			</div>	
		</section>
		<!-- End services Area -->
		
		<!-- Start review Area -->
		<section class="review-area section-gap" id="testimonial">
			<div class="container">
				<div class="row d-flex justify-content-center">
					<div class="menu-content pb-60 col-lg-10">
						<div class="title text-center">
							<h1 class="mb-10">How Our Customers felt for Us</h1>
							<p>Who are in extreme love with friendly &amp; inviting people</p>
						</div>
					</div>
				</div>							
				<div class="row">
					<div class="col-lg-6">
						<div class="single-review">
							<img src="img/c1.png" alt="">
							<div class="title d-flex flex-row">
								<a href="#"><h4>Pratik Parmar</h4></a>									
								<div class="star">
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
								</div>
							</div>
							<p>
								It was a life Experience to visit such a beautiful cultural country.
							</p>
						</div>	
						<div class="single-review">
							<img src="img/c3.png" alt="">
							<div class="title d-flex flex-row">
								<a href="#"><h4>Rahul Vankar</h4></a>									
								<div class="star">
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
								</div>
							</div>
							<p>
								Just one Word - Incredible!
							</p>
						</div>															
					</div>
					<div class="col-lg-6">
						<div class="single-review">
							<img src="img/c2.png" alt="">
							<div class="title d-flex flex-row">
								<a href="https://www.instagram.com/mr_jadav03/"><h4>Brijesh Jadav</h4></a>									
								<div class="star">
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-starchecked"></span>
									<span class="fa fa-star checked"></span>
								</div>
							</div>
							<p>
								Damm! I'm thinking of staying here in this World.
							</p>
						</div>	
						<div class="single-review">
							<img src="img/c4.png" alt="">
							<div class="title d-flex flex-row">
								<a href="#"><h4>Jackie Chan</h4></a>									
								<div class="star">
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
									<span class="fa fa-star checked"></span>
								</div>
							</div>
							<p>
								Now, I am big fan of Power Yoga. Thanks for such a gift to this world.
							</p>
						</div>															
					</div>							
				</div>
			</div>	
		</section>
		<!-- End review Area -->
		

		<!-- start footer Area -->		
		<footer class="footer-area section-gap">
			<div class="container">
				<div class="row">
					<div class="col-lg-5 col-md-6 col-sm-6">
						<div class="single-footer-widget">
							<h6>About Us</h6>
							<p>
								We Provide Tour Plans for your most comfortable travel ever. Let yourself dive into the Beauty, Culture &amp; festivals of India. Have You're Good time at India.
							</p>
							<p class="footer-text">
								<i class="fa fa-heart-o" aria-hidden="true"></i> by <a href="https://github.com/codewithdhruval" target="_blank">Dhruval Vaishya</a>
						</p>	
						</div>
					</div>
					<div class="col-lg-5  col-md-6 col-sm-6">
						<div class="single-footer-widget">
							<h6>Newsletter</h6>
							<p>Stay update with our latest</p>
							<div class="" id="mc_embed_signup">
							 <form target="_blank"  action="" method="get" class="form-inline">
									<input class="form-control" name="EMAIL" placeholder="Enter Email" onfocus="this.placeholder = ''" onblur="this.placeholder = 'Enter Your Email Here '" required="" type="email">
										<button class="click-btn btn btn-default"><i class="fa fa-long-arrow-right" aria-hidden="true"></i></button>
								   
									<div class="info"></div>
								</form>
							</div>
						</div>
					</div>						
					<div class="col-lg-2 col-md-6 col-sm-6 social-widget">
						<div class="single-footer-widget">
							<h6>Follow Us</h6>
							<p>Let us be social</p>
							<div class="footer-social d-flex align-items-center">
								<a href="#"><i class="fa fa-facebook"></i></a>
								<a href="#"><i class="fa fa-twitter"></i></a>
								<a href="#"><i class="fa fa-snapchat"></i></a>
								<a href="https://www.instagram.com/mr_akki_070/"><i class="fa fa-instagram"></i></a>
							</div>
						</div>
					</div>							
				</div>
			</div>
		</footer>	
		<!-- End footer Area -->					
	</div>

	<script src="{{ url_for('static', filename='js/vendor/jquery-2.2.4.min.js') }}"></script>
	<script src="{{ url_for('static', filename='https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js') }}"> integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
	<script src="{{ url_for('static', filename='js/vendor/bootstrap.min.js') }}"></script>
	<script src="{{ url_for('static', filename='js/easing.min.js') }}"></script>
	<script src="{{ url_for('static', filename='js/jquery.ajaxchimp.min.js') }}"></script>
	<script src="{{ url_for('static', filename='js/jquery.magnific-popup.min.js') }}"></script>
	<script src="{{ url_for('static', filename='js/owl.carousel.min.js') }}"></script>
	<script src="{{ url_for('static', filename='js/jquery.sticky.js') }}"></script>
	<script src="{{ url_for('static', filename='js/jquery.nice-select.min.js') }}"></script>
	<script src="{{ url_for('static', filename='js/parallax.min.js') }}"></script>
	<script src="{{ url_for('static', filename='js/mail-script.js') }}"></script>
	<script src="{{ url_for('static', filename='js/isotope.pkgd.min.js') }}"></script>
	<script src="{{ url_for('static', filename='js/main.js') }}"></script>
	

	<!-- <script src="js/vendor/jquery-2.2.4.min.js"></script>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
	<script src="js/vendor/bootstrap.min.js"></script>			
	  <script src="js/easing.min.js"></script>			
	<script src="js/jquery.ajaxchimp.min.js"></script>
	<script src="js/jquery.magnific-popup.min.js"></script>	
	<script src="js/owl.carousel.min.js"></script>			
	<script src="js/jquery.sticky.js"></script>			
	<script src="js/jquery.nice-select.min.js"></script>			
	<script src="js/parallax.min.js"></script>	
	<script src="js/mail-script.js"></script>
	<script src="js/isotope.pkgd.min.js"></script>	
	<script src="js/main.js"></script>	-->

</div>
</body>
</html>





login.html
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>User Login Form</title>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/css/bootstrap.min.css">
</head>
<body>	
<div class="container">
	<h2>User Login</h2>
	<form action="{{ url_for('login') }}" method="post">
	    {% if mesage is defined and mesage %}
			<div class="alert alert-warning">{{ mesage }}</div>
		{% endif %}
		<div class="form-group">
			<label for="email">Email:</label>
			<input type="email" class="form-control" id="email" name="email" placeholder="Enter email" name="email">
		</div>
		<div class="form-group">
			<label for="pwd">Password:</label>
			<input type="password" class="form-control" id="password" name="password" placeholder="Enter password" name="pswd">
		</div>    
		<button type="submit" class="btn btn-primary">Login</button>
		<p class="bottom">Dont't have an account?  <a class="bottom" href="{{url_for('register')}}"> Register here</a></p>
	</form>
</div>
</body>
</html>





register.html
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>User Registeration Form</title>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/css/bootstrap.min.css">              
</head>
<body>
<div class="container">
	<h2>User Registration</h2>
	<form action="{{ url_for('register') }}" method="post">
        {% if mesage is defined and mesage %}
			<div class="alert alert-warning">{{ mesage }}</div>
		{% endif %}
		<div class="form-group">
			<label for="name">Name:</label>
			<input type="text" class="form-control" id="name" name="name" placeholder="Enter name" name="name">
		</div>
		<div class="form-group">
			<label for="email">Email:</label>
			<input type="email" class="form-control" id="email" name="email" placeholder="Enter email" name="email">
		</div>
		<div class="form-group">
			<label for="pwd">Password:</label>
			<input type="password" class="form-control" id="password" name="password" placeholder="Enter password" name="pswd">
		</div>    
		<button type="submit" class="btn btn-primary">Register</button>
		<p class="bottom">Already have an account?  <a class="bottom" href="{{url_for('login')}}"> Login here</a></p>
	</form>
</div>        
</body>
</html>
