# የፍለተር አፕሊኬሽን ለመገንባት የምንጠቀማቸውን flutter widgets እንደ snippet አዘጋጅቼላችኋለው
code snippet meaning: A code snippet is a small portion of code that represents a specific functionality or task.

<p > <H1 align = "center">topics to cover</H1> </p>
<p align = "left"> onboarding</p>
<p align = "left"> splash </p>
<p align = "left"> [signup and signin with reset]  (https://dev.to/josephyaduvanshi/flutter-beautiful-loginsignup-responsive-ui-design-with-animation-22b7)</p>
<p align = "left"> bottom navigation </p>
<p align = "left"> listview and detail view</p>
<p align = "left">  add to cart feature</p>
<p align = "left">  map feature</p>




# onboarding page leading to splash screen








## Getting Started

The significance of an onboarding page in a mobile application is to provide a welcoming and informative introduction to the app for new users. It helps users understand the app's purpose, features, and benefits, reducing the learning curve and increasing user engagement, leading to a positive initial experience.

ሞባይል አፕፕሊኬሽኖችን ስንጠቀም በውስጣቸው ያለውን አዳዲስ እና ልዩ ብቃቶችን የምናሳይበት ቦታ onboading page በመባል የሚታወቅ ሲሆን ለአፕሊኬሽኑ ተጠቃሚ የኛን አፕሊኬሽን በመጠቀሙ እና በመምረጡ ምን ያህል እንደሚጠቀም የምናሳምንበት ቦታ ነው።
የሚከተለው ኮድ ይህን እንድናደርግ ይረዳናል።


```
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  SharedPreferences prefs = await SharedPreferences.getInstance();
  bool onboardingShown = prefs.getBool('onboarding_shown') ?? false;

  runApp(MyApp(onboardingShown: onboardingShown));
}

class MyApp extends StatelessWidget {
  final bool onboardingShown;

  MyApp({required this.onboardingShown});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: onboardingShown ? SplashScreen() : OnboardingPage(),
    );
  }
}

class SplashScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child:
            CircularProgressIndicator(), // Replace this with your splash screen UI
      ),
    );
  }
}

class OnboardingPage extends StatefulWidget {
  @override
  _OnboardingPageState createState() => _OnboardingPageState();
}

class _OnboardingPageState extends State<OnboardingPage> {
  final PageController _pageController = PageController(initialPage: 0);
  int _currentPage = 0;
  bool _isLastPage = false;

  @override
  void initState() {
    super.initState();
    _setOnboardingStatus();
  }

  Future<void> _setOnboardingStatus() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    prefs.setBool('onboarding_shown', true);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        leading: _currentPage != 0
            ? IconButton(
                icon: Icon(Icons.arrow_back),
                onPressed: () {
                  _pageController.previousPage(
                    duration: Duration(milliseconds: 500),
                    curve: Curves.ease,
                  );
                },
              )
            : null,
      ),
      body: PageView(
        controller: _pageController,
        onPageChanged: (index) {
          setState(() {
            _currentPage = index;
            _isLastPage =
                index == 2; // Update this value if you have more pages
          });
        },
        children: [
          OnboardingScreen(
            title: 'Page 1',
            description: 'Your content for Page 1',
          ),
          OnboardingScreen(
            title: 'Page 2',
            description: 'Your content for Page 2',
          ),
          OnboardingScreen(
            title: 'Page 3',
            description: 'Your content for Page 3',
          ),
        ],
      ),
      bottomNavigationBar: _buildBottomNavigationBar(),
    );
  }

  Widget _buildBottomNavigationBar() {
    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          if (_currentPage != 0)
            ElevatedButton(
              onPressed: () {
                _pageController.previousPage(
                  duration: Duration(milliseconds: 500),
                  curve: Curves.ease,
                );
              },
              child: Text('Previous'),
            ),
          ElevatedButton(
            onPressed: _isLastPage ? _finishOnboarding : _nextPage,
            child: Text(_isLastPage ? 'Finish' : 'Next'),
          ),
        ],
      ),
    );
  }

  void _nextPage() {
    _pageController.nextPage(
      duration: Duration(milliseconds: 500),
      curve: Curves.ease,
    );
  }

  void _finishOnboarding() {
    // Implement what to do when the user finishes onboarding
    // For example, you can navigate to the home screen:
    // Navigator.pushReplacement(context, MaterialPageRoute(builder: (context) => HomeScreen()));
   
  }
}

class OnboardingScreen extends StatelessWidget {
  final String title;
  final String description;

  OnboardingScreen({required this.title, required this.description});

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Text(
            title,
            style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
          ),
          SizedBox(height: 16),
          Text(
            description,
            textAlign: TextAlign.center,
            style: TextStyle(fontSize: 18),
          ),
        ],
      ),
    );
  }
}
```


# to make more interactive and include lotte animation 

```
import 'package:flutter/material.dart';
import 'package:lottie/lottie.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Onboarding App',
      home: FutureBuilder<bool>(
        future: _checkFirstSeen(),
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return SplashScreen(); // Show the splash screen while checking onboarding status
          } else {
            if (snapshot.data != true) {
              return OnboardingScreen(); // Show the splash screen after onboarding is completed
            } else {
              return SplashScreen(); // Show onboarding screens
            }
          }
        },
      ),
      theme: ThemeData(
        //primaryColor: Colors.orange, // Set the primary color to orange
        useMaterial3: true,
      ),
    );
  }

  Future<bool> _checkFirstSeen() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    bool isOnboardingCompleted =
        prefs.getBool('isOnboardingCompleted') ?? false;
    return isOnboardingCompleted;
  }
}

class OnboardingScreen extends StatefulWidget {
  @override
  _OnboardingScreenState createState() => _OnboardingScreenState();
}

class _OnboardingScreenState extends State<OnboardingScreen> {
  final PageController _pageController = PageController(initialPage: 0);
  int _currentPage = 0;
  int _numPages = 3;

  final List<String> titles = [
    'Welcome to Onboarding Screen 1',
    'Welcome to Onboarding Screen 2',
    'Welcome to Onboarding Screen 3',
  ];

  final List<String> descriptions = [
    'This is the description for Onboarding Screen 1.',
    'This is the description for Onboarding Screen 2.',
    'This is the description for Onboarding Screen 3.',
  ];

  @override
  void initState() {
    super.initState();
  }

  Future<void> _finishedOnboarding() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    prefs.setBool('isOnboardingCompleted', true);
  }

  void _onPageChanged(int page) {
    setState(() {
      _currentPage = page;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      // backgroundColor: Colors.orange, // Set the background color to orange
      body: Stack(
        children: [
          Positioned(
            bottom: 16,
            left: 0,
            right: 0,
            child: Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: List.generate(_numPages, (index) {
                return AnimatedContainer(
                  duration: Duration(milliseconds: 300),
                  width: _currentPage == index ? 12 : 8,
                  height: 8,
                  margin: EdgeInsets.symmetric(horizontal: 4),
                  decoration: BoxDecoration(
                    color: _currentPage == index ? Colors.orange : Colors.grey,
                    shape: BoxShape.circle,
                  ),
                );
              }),
            ),
          ),
          PageView.builder(
            controller: _pageController,
            onPageChanged: _onPageChanged,
            itemCount: _numPages,
            itemBuilder: (context, index) {
              return _buildOnboardingPage(index);
            },
          ),
        ],
      ),
    );
  }

  Widget _buildOnboardingPage(int index) {
    String title = titles[index];
    String description = descriptions[index];
    String animationAsset = '';

    switch (index) {
      case 0:
        animationAsset = "assets/animationone.json";
        break;
      case 1:
        animationAsset = "assets/animationtwo.json";
        break;
      case 2:
        animationAsset = "assets/animationthree.json";
        break;
      default:
        animationAsset = '';
    }

    bool isFirstPage = index == 0;
    bool isLastPage = index == _numPages - 1;

    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Lottie.asset(animationAsset, height: 200, width: 200),
          SizedBox(height: 30),
          Text(
            title,
            style: TextStyle(
              fontSize: 24,
              fontWeight: FontWeight.bold,
              color: Colors.orange, // Set the title color to white
            ),
          ),
          SizedBox(height: 16),
          Text(
            description,
            style: TextStyle(
                fontSize: 16,
                color: Colors.orange), // Set the description color to white
            textAlign: TextAlign.center,
          ),
          SizedBox(height: 30),
          Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              if (!isFirstPage)
                Padding(
                  padding: const EdgeInsets.all(8.0),
                  child: ElevatedButton(
                    onPressed: () {
                      _pageController.previousPage(
                        duration: Duration(milliseconds: 300),
                        curve: Curves.easeInOut,
                      );
                    },
                    child: Text('<'),
                    style: ElevatedButton.styleFrom(
                      primary: Colors
                          .white, // Set the button background color to white
                      onPrimary:
                          Colors.orange, // Set the button text color to orange
                      padding: EdgeInsets.symmetric(
                          horizontal: 50, vertical: 12), // Adjust the spacing
                    ),
                  ),
                ),
              if (!isLastPage)
                ElevatedButton(
                  onPressed: () {
                    _pageController.nextPage(
                      duration: Duration(milliseconds: 300),
                      curve: Curves.easeInOut,
                    );
                  },
                  child: Text('>'),
                  style: ElevatedButton.styleFrom(
                    primary: Colors
                        .white, // Set the button background color to white
                    onPrimary:
                        Colors.orange, // Set the button text color to orange
                    padding: EdgeInsets.symmetric(
                        horizontal: 50, vertical: 12), // Adjust the spacing
                  ),
                ),
              if (isLastPage)
                Padding(
                  padding: const EdgeInsets.all(8.0),
                  child: ElevatedButton(
                    onPressed: () {
                      _finishedOnboarding();
                      // Navigate to the splash screen after onboarding is completed
                      Navigator.pushReplacement(
                        context,
                        MaterialPageRoute(builder: (context) => SplashScreen()),
                      );
                    },
                    child: Text('Finish'),
                    style: ElevatedButton.styleFrom(
                      primary: Colors
                          .white, // Set the button background color to white
                      onPrimary:
                          Colors.orange, // Set the button text color to orange
                      padding: EdgeInsets.symmetric(
                          horizontal: 50, vertical: 12), // Adjust the spacing
                    ),
                  ),
                ),
            ],
          ),
        ],
      ),
    );
  }
}


```

in order to use it on your code just follow the following steps 

step 1.download 3 json lottie animation and name them animationone.json animationtwo.json animationthree.json and also make sure that you add them in the assets folder </br>
step 2. copy the above code </br>
step 3. add lottie , shared preference and others that indicate red light 



# splash screen 
The splash screen in a mobile application serves as an initial loading screen or an introductory image that appears when the app is launched. Its significance lies in the following aspects:</br>

Branding and User Experience: A well-designed splash screen helps establish the app's branding, creating a consistent and memorable experience for users.

First Impression: The splash screen provides the first impression of the app, giving developers a chance to showcase their creativity and make a positive impact on users from the very beginning.

App Loading: During the splash screen display, the app's essential resources can be initialized and loaded in the background, optimizing the user's waiting time.

App Perception: A professional-looking splash screen can imply that the app is reliable and well-built, potentially increasing user trust.

App Transition: The splash screen acts as a smooth transition between the app launch and the app's main interface, preventing abrupt or jarring loading experiences.



```

class SplashScreen extends StatefulWidget {
  @override
  _SplashScreenState createState() => _SplashScreenState();
}

class _SplashScreenState extends State<SplashScreen> {
  @override
  void initState() {
    super.initState();
    Timer(
        Duration(seconds: 3),
        () => Navigator.pushReplacement(
            context, MaterialPageRoute(builder: (context) => HomeScreen())));
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        width: double.infinity,
        height: double.infinity,
        // decoration: BoxDecoration(
        //   gradient: LinearGradient(
        //       begin: Alignment.topRight,
        //       end: Alignment.bottomLeft,
        //       colors: [
        //         Color(0xFFFF800B),
        //         Color(0xFFCE1010),
        //       ]),
        // ),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.center,
          mainAxisAlignment: MainAxisAlignment.spaceAround,
          children: [
            Column(
              children: [
                Image.asset(
                  "assets/images/img-1.jpg",
                  height: 300.0,
                  width: 300.0,
                ),
                Text(
                  "A whole grocery store\n at your fingertips",
                  textAlign: TextAlign.center,
                  style: TextStyle(
                    color: Color.fromARGB(255, 50, 0, 0),
                    fontWeight: FontWeight.bold,
                    fontSize: 18.0,
                  ),
                ),
              ],
            ),
            CircularProgressIndicator(
              valueColor: AlwaysStoppedAnimation<Color>(Colors.orange),
            ),
          ],
        ),
      ),
    );
  }
}
```




# sign up sign in and forgot password 
the significance of it is 
Sign In: The "Sign In" screen allows users who have already registered or created an account to access their personalized content, settings, and data. It provides a secure entry point to the app, ensuring that only authorized users can access their accounts and interact with the app's features.

Sign Up: The "Sign Up" screen is where new users can create accounts and register with the application. It serves as the onboarding process, enabling users to become a part of the app's user base. By providing their details, they gain access to personalized features and data, tailored to their preferences.

Forgot Password: The "Forgot Password" screen offers a way for users who have forgotten their account credentials to regain access to their accounts. It typically involves a password reset mechanism, such as sending a verification email or text message with a link to reset the password. This feature enhances user convenience and prevents the frustration of permanently losing access to their accounts due to forgotten passwords.
```

class LoginScreen extends StatefulWidget {
  const LoginScreen({Key? key}) : super(key: key);

  @override
  State<LoginScreen> createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  @override
  Widget build(BuildContext context) {
    return Material(
      color: Colors.white,
      child: SafeArea(
        child: SingleChildScrollView(
          child: Column(
            children: [
              Image.asset(
                'assets/images/jebena.png',
                fit: BoxFit.fill,
              ),
              Lottie.asset('assets/animationone.json', height: 200, width: 200),
              Padding(
                padding: const EdgeInsets.symmetric(
                  vertical: 16,
                  horizontal: 32,
                ),
                child: Column(
                  children: [
                    TextFormField(
                      decoration: const InputDecoration(
                        icon: Icon(Icons.email),
                        hintText: 'Enter Your Username/Email',
                        labelText: 'Email or Username',
                      ),
                      onChanged: (value) {
                        setState(() {});
                      },
                    ),
                    TextFormField(
                      obscureText: true,
                      decoration: const InputDecoration(
                        icon: Icon(Icons.lock),
                        hintText: 'Enter Your Password',
                        labelText: 'Password',
                      ),
                      onChanged: (value) {
                        setState(() {});
                      },
                    ),
                    TextButton(
                      onPressed: () {
                        //Navigator.pushNamed(context, MyRoutes.forgotPassword);
                        Navigator.push(
                          context,
                          MaterialPageRoute(
                              builder: (context) => ForgotPassword()),
                        );
                      },
                      child: const Text(
                        'Forgot Password?',
                      ),
                    ),
                    TextButton.icon(
                      onPressed: (() {
                        //Navigator.pushNamed(context, MyRoutes.homeScreen);
                        Navigator.push(
                          context,
                          MaterialPageRoute(builder: (context) => HomeScreen()),
                        );
                      }),
                      icon: const Icon(Icons.login),
                      label: Container(
                        alignment: Alignment.center,
                        width: 150,
                        height: 35,
                        child: const Text(
                          'Sign In',
                          style: TextStyle(
                            fontSize: 18,
                            color: Colors.white,
                          ),
                        ),
                        decoration: BoxDecoration(
                          color: Colors.red,
                          borderRadius: BorderRadius.circular(25),
                        ),
                      ),
                    ),
                    Row(
                      children: [
                        const Text('Don\'t have an account?'),
                        TextButton(
                          onPressed: (() {
                            // Navigator.pushNamed(
                            //   context,
                            //   MyRoutes.signUp,
                            // );
                            Navigator.push(
                              context,
                              MaterialPageRoute(builder: (context) => SignUp()),
                            );
                          }),
                          child: const Text(
                            'Sign Up',
                            style: TextStyle(
                              fontSize: 15,
                              fontWeight: FontWeight.bold,
                            ),
                          ),
                        ),
                      ],
                      mainAxisAlignment: MainAxisAlignment.center,
                    ),
                  ],
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

class SignUp extends StatefulWidget {
  const SignUp({Key? key}) : super(key: key);

  @override
  State<SignUp> createState() => _SignUpState();
}

class _SignUpState extends State<SignUp> {
  @override
  Widget build(BuildContext context) {
    return Material(
      color: Colors.white,
      child: SafeArea(
        child: Column(
          children: [
            Image.asset(
              'assets/images/signup.png',
              fit: BoxFit.cover,
              height: 300,
              width: 350,
            ),
            const SizedBox(
              height: 20,
              width: 20,
            ),
            const Text(
              'Create Account',
              style: TextStyle(
                fontSize: 30,
                fontFamily: 'Courier',
                color: Colors.red,
                fontWeight: FontWeight.bold,
              ),
              textAlign: TextAlign.center,
            ),
            Padding(
              padding: const EdgeInsets.symmetric(
                vertical: 16,
                horizontal: 32,
              ),
              child: Column(
                children: [
                  TextFormField(
                    decoration: const InputDecoration(
                      icon: Icon(Icons.person),
                      hintText: 'Enter Your Full Name',
                      labelText: 'Full Name',
                    ),
                  ),
                  const SizedBox(
                    height: 10,
                    width: 10,
                  ),
                  TextFormField(
                    decoration: const InputDecoration(
                      icon: Icon(Icons.email),
                      hintText: 'Enter Your Email/Username',
                      labelText: 'Email or Username',
                    ),
                  ),
                  const SizedBox(
                    height: 10,
                    width: 10,
                  ),
                  TextFormField(
                    obscureText: true,
                    decoration: const InputDecoration(
                      icon: Icon(Icons.lock),
                      hintText: 'Enter Your Password',
                      labelText: 'Password',
                    ),
                  ),
                  const SizedBox(
                    height: 10,
                    width: 10,
                  ),
                  TextButton.icon(
                    onPressed: (() {
                      //sign up
                    }),
                    icon: const Icon(Icons.create),
                    label: Container(
                      alignment: Alignment.center,
                      width: 150,
                      height: 35,
                      child: const Text(
                        'Sign Up',
                        style: TextStyle(
                          fontSize: 18,
                          color: Colors.white,
                        ),
                      ),
                      decoration: BoxDecoration(
                        color: Colors.red,
                        borderRadius: BorderRadius.circular(25),
                      ),
                    ),
                  ),
                  Row(
                    children: [
                      const Text('Already have an account?'),
                      TextButton(
                        onPressed: (() {
                          // Navigator.pushNamed(
                          //   context,
                          //   MyRoutes.loginScreen,
                          // );
                          Navigator.push(
                            context,
                            MaterialPageRoute(
                                builder: (context) => LoginScreen()),
                          );
                        }),
                        child: const Text(
                          'Sign In',
                          style: TextStyle(
                            fontSize: 15,
                            fontWeight: FontWeight.bold,
                          ),
                        ),
                      ),
                    ],
                    mainAxisAlignment: MainAxisAlignment.center,
                  ),
                  const Text(
                    'By signing up you agree to our terms, conditions and privacy Policy.',
                    style: TextStyle(
                      fontSize: 13,
                    ),
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class ForgotPassword extends StatefulWidget {
  const ForgotPassword({Key? key}) : super(key: key);

  @override
  State<ForgotPassword> createState() => _ForgotPasswordState();
}

class _ForgotPasswordState extends State<ForgotPassword> {
  @override
  Widget build(BuildContext context) {
    return Material(
      color: Colors.white,
      child: SafeArea(
        child: Column(
          children: [
            Image.asset(
              'assets/images/forgot.png',
              fit: BoxFit.cover,
            ),
            const SizedBox(
              height: 35,
              width: 30,
            ),
            const Center(
              child: Text(
                'Send Reset Link to Email!',
                style: TextStyle(
                  fontSize: 22,
                  color: Colors.red,
                  fontWeight: FontWeight.bold,
                  fontFamily: 'Courier',
                ),
              ),
            ),
            Padding(
              padding: const EdgeInsets.symmetric(
                vertical: 32,
                horizontal: 16,
              ),
              child: Column(
                children: [
                  TextFormField(
                    decoration: const InputDecoration(
                      hintText: 'Enter Your Email',
                      labelText: 'Email',
                      border: OutlineInputBorder(),
                    ),
                  ),
                  const SizedBox(
                    height: 10,
                    width: 10,
                  ),
                  TextButton.icon(
                    onPressed: (() {}),
                    icon: const Icon(
                      Icons.read_more,
                      size: 28,
                    ),
                    label: Container(
                      alignment: Alignment.center,
                      width: 150,
                      height: 35,
                      child: const Text(
                        'Send Reset Link',
                        style: TextStyle(
                          fontSize: 12,
                          color: Colors.white,
                        ),
                      ),
                      decoration: BoxDecoration(
                        color: Colors.red,
                        borderRadius: BorderRadius.circular(25),
                      ),
                    ),
                  ),
                  const SizedBox(
                    height: 10,
                    width: 10,
                  ),
                  TextButton.icon(
                    onPressed: (() {
                      //   Navigator.pushNamed(context, MyRoutes.loginScreen);
                      Navigator.push(
                        context,
                        MaterialPageRoute(builder: (context) => LoginScreen()),
                      );
                    }),
                    icon: const Icon(
                      Icons.home,
                      size: 28,
                    ),
                    label: Container(
                      alignment: Alignment.center,
                      width: 150,
                      height: 35,
                      child: const Text(
                        'Return Home',
                        style: TextStyle(
                          fontSize: 12,
                          color: Colors.white,
                        ),
                      ),
                      decoration: BoxDecoration(
                        color: Colors.red,
                        borderRadius: BorderRadius.circular(25),
                      ),
                    ),
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```


# botton navigation and navigation drawer with appbar and listview with detail view 

```
class HomeScreen extends StatefulWidget {
  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: getBody(),
      bottomNavigationBar: _buildBottomBar(),
    );
  }

  int _currentIndex = 0;
  final _inactiveColor = Colors.grey;
  Widget _buildBottomBar() {
    return CustomAnimatedBottomBar(
      containerHeight: 40,
      //backgroundColor: Colors.white38,
      selectedIndex: _currentIndex,
      showElevation: true,
      itemCornerRadius: 24,
      curve: Curves.easeIn,
      onItemSelected: (index) => setState(() => _currentIndex = index),
      items: <BottomNavyBarItem>[
        BottomNavyBarItem(
          icon: Icon(Icons.apps),
          title: Text('Home'),
          activeColor: Colors.green,
          inactiveColor: _inactiveColor,
          textAlign: TextAlign.center,
        ),
        BottomNavyBarItem(
          icon: Icon(Icons.people),
          title: Text('Users'),
          activeColor: Colors.purpleAccent,
          inactiveColor: _inactiveColor,
          textAlign: TextAlign.center,
        ),
        BottomNavyBarItem(
          icon: Icon(Icons.message),
          title: Text(
            'Messages ',
          ),
          activeColor: Colors.pink,
          inactiveColor: _inactiveColor,
          textAlign: TextAlign.center,
        ),
        BottomNavyBarItem(
          icon: Icon(Icons.settings),
          title: Text('Settings'),
          activeColor: Colors.blue,
          inactiveColor: _inactiveColor,
          textAlign: TextAlign.center,
        ),
      ],
    );
  }

  Widget getBody() {
    List<Widget> pages = [
      // Container(
      //   alignment: Alignment.center,
      //   child: Text(
      //     "Home",
      //     style: TextStyle(fontSize: 25, fontWeight: FontWeight.bold),
      //   ),
      // )
      HomePage1(),
      Container(
          alignment: Alignment.center,
          child: GridView.builder(
            itemCount: 100,
            itemBuilder: (context, index) => ItemTile(index),
            gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
              crossAxisCount: 2,
              childAspectRatio: 2,
            ),
          )),

      Container(
        alignment: Alignment.center,
        child: Text(
          "Messages",
          style: TextStyle(fontSize: 25, fontWeight: FontWeight.bold),
        ),
      ),
      Container(
          alignment: Alignment.center,
          child: InkWell(
            onTap: () {},
            child: Text(
              "Settings",
              style: TextStyle(fontSize: 25, fontWeight: FontWeight.bold),
            ),
          )),
    ];
    return IndexedStack(
      index: _currentIndex,
      children: pages,
    );
  }
}

class ItemTile extends StatelessWidget {
  final int itemNo;
  const ItemTile(
    this.itemNo,
  );
  @override
  Widget build(BuildContext context) {
    final Color color = Colors.primaries[itemNo % Colors.primaries.length];
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: ListTile(
        tileColor: color.withOpacity(0.3),
        onTap: () {},
        leading: Container(
          width: 50,
          height: 30,
          color: color.withOpacity(0.5),
          child: Placeholder(
            color: color,
          ),
        ),
        title: Text(
          'Product itemNo',
          key: Key('text_itemNo'),
        ),
      ),
    );
  }
}

class CustomAnimatedBottomBar extends StatelessWidget {
  CustomAnimatedBottomBar({
    Key? key,
    this.selectedIndex = 0,
    this.showElevation = true,
    this.iconSize = 24,
    this.backgroundColor,
    this.itemCornerRadius = 50,
    this.containerHeight = 56,
    this.animationDuration = const Duration(milliseconds: 270),
    this.mainAxisAlignment = MainAxisAlignment.spaceBetween,
    required this.items,
    required this.onItemSelected,
    this.curve = Curves.linear,
  })  : assert(items.length >= 2 && items.length <= 5),
        super(key: key);

  final int selectedIndex;
  final double iconSize;
  final Color? backgroundColor;
  final bool showElevation;
  final Duration animationDuration;
  final List<BottomNavyBarItem> items;
  final ValueChanged<int> onItemSelected;
  final MainAxisAlignment mainAxisAlignment;
  final double itemCornerRadius;
  final double containerHeight;
  final Curve curve;

  @override
  Widget build(BuildContext context) {
    final bgColor = backgroundColor ?? Theme.of(context).bottomAppBarColor;

    return Container(
      decoration: BoxDecoration(
        color: bgColor,
        boxShadow: [
          if (showElevation)
            const BoxShadow(
              color: Colors.black12,
              blurRadius: 2,
            ),
        ],
      ),
      child: SafeArea(
        child: Container(
          width: double.infinity,
          height: containerHeight,
          padding: const EdgeInsets.symmetric(vertical: 6, horizontal: 8),
          child: Row(
            mainAxisAlignment: mainAxisAlignment,
            children: items.map((item) {
              var index = items.indexOf(item);
              return GestureDetector(
                onTap: () => onItemSelected(index),
                child: _ItemWidget(
                  item: item,
                  iconSize: iconSize,
                  isSelected: index == selectedIndex,
                  backgroundColor: bgColor,
                  itemCornerRadius: itemCornerRadius,
                  animationDuration: animationDuration,
                  curve: curve,
                ),
              );
            }).toList(),
          ),
        ),
      ),
    );
  }
}

class _ItemWidget extends StatelessWidget {
  final double iconSize;
  final bool isSelected;
  final BottomNavyBarItem item;
  final Color backgroundColor;
  final double itemCornerRadius;
  final Duration animationDuration;
  final Curve curve;

  const _ItemWidget({
    Key? key,
    required this.item,
    required this.isSelected,
    required this.backgroundColor,
    required this.animationDuration,
    required this.itemCornerRadius,
    required this.iconSize,
    this.curve = Curves.linear,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Semantics(
      container: true,
      selected: isSelected,
      child: AnimatedContainer(
        width: isSelected ? 130 : 50,
        height: double.maxFinite,
        duration: animationDuration,
        curve: curve,
        decoration: BoxDecoration(
          color:
              isSelected ? item.activeColor.withOpacity(0.2) : backgroundColor,
          borderRadius: BorderRadius.circular(itemCornerRadius),
        ),
        child: SingleChildScrollView(
          scrollDirection: Axis.horizontal,
          physics: NeverScrollableScrollPhysics(),
          child: Container(
            width: isSelected ? 130 : 50,
            padding: EdgeInsets.symmetric(horizontal: 8),
            child: Row(
              mainAxisSize: MainAxisSize.max,
              mainAxisAlignment: MainAxisAlignment.start,
              crossAxisAlignment: CrossAxisAlignment.center,
              children: <Widget>[
                IconTheme(
                  data: IconThemeData(
                    size: iconSize,
                    color: isSelected
                        ? item.activeColor.withOpacity(1)
                        : item.inactiveColor == null
                            ? item.activeColor
                            : item.inactiveColor,
                  ),
                  child: item.icon,
                ),
                if (isSelected)
                  Expanded(
                    child: Container(
                      padding: EdgeInsets.symmetric(horizontal: 4),
                      child: DefaultTextStyle.merge(
                        style: TextStyle(
                          color: item.activeColor,
                          fontWeight: FontWeight.bold,
                        ),
                        maxLines: 1,
                        textAlign: item.textAlign,
                        child: item.title,
                      ),
                    ),
                  ),
              ],
            ),
          ),
        ),
      ),
    );
  }
}

class BottomNavyBarItem {
  BottomNavyBarItem({
    required this.icon,
    required this.title,
    this.activeColor = Colors.blue,
    this.textAlign,
    this.inactiveColor,
  });

  final Widget icon;
  final Widget title;
  final Color activeColor;
  final Color? inactiveColor;
  final TextAlign? textAlign;
}

// first screen with detail

class FoodItem {
  final String title;
  final String subtitle;
  final String imageUrl;
  final List<String> characteristics;

  FoodItem(
      {required this.title,
      required this.subtitle,
      required this.imageUrl,
      required this.characteristics});
}

class HomePage1 extends StatelessWidget {
  List<FoodItem> itemList = [
    FoodItem(
      title: "Pizza",
      subtitle: "Delicious pizza with toppings",
      imageUrl: "assets/images/pizza.jpg",
      characteristics: ["Cheesy", "Savory", "Crispy"],
    ),
    FoodItem(
      title: "Burger",
      subtitle: "Juicy burger with fries",
      imageUrl: "assets/images/burger.jpg",
      characteristics: ["Meaty", "Satisfying", "Tasty"],
    ),
    FoodItem(
      title: "Sushi",
      subtitle: "Fresh sushi rolls with soy sauce",
      imageUrl: "assets/images/sushi.jpg",
      characteristics: ["Raw", "Healthy", "Exotic"],
    ),
    FoodItem(
      title: "shiro",
      subtitle: "yabede shiro ",
      imageUrl: "assets/images/sushi.jpg",
      characteristics: ["Raw", "Healthy", "Exotic"],
    ),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        centerTitle: true,
        title: Text("hello"),
        actions: [
          IconButton(
            icon: Icon(Icons.search),
            onPressed: () {
              // Handle search action here
            },
          ),
          IconButton(
            icon: Icon(Icons.notifications),
            onPressed: () {
              // Handle notifications action here
            },
          ),
          IconButton(
            icon: Icon(Icons.settings),
            onPressed: () {
              // Handle settings action here
            },
          ),
          PopupMenuButton<int>(
            itemBuilder: (context) => [
              PopupMenuItem<int>(
                value: 1,
                child: ListTile(
                  leading: Icon(Icons.search),

                  title: Text('Search'),
                  //onTap: () => _onSearchPressed(context),
                ),
              ),
              PopupMenuItem<int>(
                value: 2,
                child: ListTile(
                  leading: Icon(Icons.notification_add),
                  title: Text('Notifications'),
                  //onTap: () => _onNotificationsPressed(context),
                ),
              ),
              PopupMenuItem<int>(
                value: 3,
                child: ListTile(
                  leading: Icon(Icons.settings),

                  title: Text('Settings'),
                  //onTap: () => _onSettingsPressed(context),
                ),
              ),
            ],
          ),
        ],
      ),
      drawer: myDrawer(context),
      body: ListView.separated(
        separatorBuilder: (context, index) => Divider(),
        itemCount: itemList.length,
        itemBuilder: (context, index) {
          return GestureDetector(
            onTap: () {
              // Navigate to detail view and pass data as arguments
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (context) => DetailView(foodItem: itemList[index]),
                ),
              );
            },
            child: Card(
              margin: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
              child: Row(
                children: [
                  Container(
                    width: 80,
                    height: 80,
                    decoration: BoxDecoration(
                      image: DecorationImage(
                        image: AssetImage(itemList[index].imageUrl),
                        fit: BoxFit.cover,
                      ),
                    ),
                  ),
                  SizedBox(width: 16),
                  Expanded(
                    child: Column(
                      crossAxisAlignment: CrossAxisAlignment.start,
                      children: [
                        Text(
                          itemList[index].title,
                          style: TextStyle(
                            fontSize: 18,
                            fontWeight: FontWeight.bold,
                          ),
                        ),
                        Text(
                          itemList[index].subtitle,
                          style: TextStyle(fontSize: 16),
                        ),
                      ],
                    ),
                  ),
                ],
              ),
            ),
          );
        },
      ),
    );
  }

  Drawer myDrawer(BuildContext context) {
    return Drawer(
      child: ListView(
        children: [
          Container(
            height: 200,
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              crossAxisAlignment: CrossAxisAlignment.center,
              children: [
                Container(
                  height: 100,
                  decoration: BoxDecoration(
                      borderRadius: BorderRadius.all(Radius.circular(50.0))),
                  child: Image.network(
                      "https://www.codewithflutter.com/wp-content/uploads/2021/05/flutter-in-tamil-01.png"),
                ),
                Text("Code With Flutter")
              ],
            ),
          ),
          Divider(),
          ListTile(
            title: Text("Interest"),
            leading: Icon(Icons.list_alt_outlined),
            onTap: () {
              ScaffoldMessenger.of(context)
                  .showSnackBar(SnackBar(content: Text("misgana i love you")));
            },
          ),
          ListTile(
            title: Text("My Courses"),
            leading: Icon(Icons.video_settings_outlined),
            onTap: () {},
          ),
          ListTile(
            title: Text("Todo/Done"),
            leading: Icon(Icons.timeline_outlined),
            onTap: () {},
          ),
          ListTile(
            title: Text("Offline Articles"),
            leading: Icon(Icons.save_alt_outlined),
            onTap: () {},
          ),
          ListTile(
            title: Text("Rate us"),
            leading: Icon(Icons.star_rate),
            onTap: () {},
          ),
          ListTile(
            title: Text("Support"),
            leading: Icon(Icons.support_agent_outlined),
            onTap: () {},
          ),
          ListTile(
            title: Text("Settings"),
            leading: Icon(Icons.settings),
            onTap: () {},
          ),
          ListTile(
            title: Text("Log out"),
            leading: Icon(Icons.logout),
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(builder: (context) => HomeScreen()),
              );
            },
          ),
        ],
      ),
    );
  }
}

class DetailView extends StatelessWidget {
  final FoodItem foodItem;

  DetailView({required this.foodItem});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text(foodItem.title),
        ),
        body: Column(children: [
          Container(
            height: 200,
            width: double.infinity,
            decoration: BoxDecoration(
              image: DecorationImage(
                image: AssetImage(foodItem.imageUrl),
                fit: BoxFit.cover,
              ),
            ),
          ),
          Padding(
              padding: EdgeInsets.all(16),
              child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Text(
                      foodItem.title,
                      style: TextStyle(
                        fontSize: 24,
                        fontWeight: FontWeight.bold,
                      ),
                    ),
                    Text(
                      foodItem.subtitle,
                      style: TextStyle(fontSize: 18),
                    ),
                    SizedBox(height: 16),
                    Text("Characteristics:",
                        style: TextStyle(
                            fontSize: 18, fontWeight: FontWeight.bold))
                  ]))
        ]));
  }
}
```

