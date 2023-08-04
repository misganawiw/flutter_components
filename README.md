# የፍለተር አፕሊኬሽን ለመገንባት የምንጠቀማቸውን flutter widgets እንደ snippet አዘጋጅቼላችኋለው
code snippet meaning: A code snippet is a small portion of code that represents a specific functionality or task.

<p > <H1 align = "center">topics to cover</H1> </p>
<p align = "left"> onboarding</p>
<p align = "left"> splash </p>
<p align = "left"> signup and signin with reset</p>
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



