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
            title: 'Fast Delivery',
            description: 'Get your food delivered in minutes!',
            animationUrl:
                'https://assets1.lottiefiles.com/private_files/lf30_QLsD8M.json',
          ),
          OnboardingScreen(
            title: 'Wide Variety',
            description: 'Choose from a wide range of delicious cuisines.',
            animationUrl:
                'https://assets1.lottiefiles.com/private_files/lf30_QLsD8M.json',
          ),
          OnboardingScreen(
            title: 'Contactless Delivery',
            description: 'Safe and contactless food delivery to your doorstep.',
            animationUrl:
                'https://assets1.lottiefiles.com/private_files/lf30_QLsD8M.json',
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
  final String animationUrl;

  OnboardingScreen(
      {required this.title,
      required this.description,
      required this.animationUrl});

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Lottie.network(
            animationUrl,
            width: 200,
            height: 200,
            fit: BoxFit.cover,
          ),
          SizedBox(height: 32),
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

class SplashScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: OnboardingScreen(
          title: 'Fast Delivery',
          description: 'Get your food delivered in minutes!',
          animationUrl:
              'https://assets1.lottiefiles.com/private_files/lf30_QLsD8M.json',
        ), // Replace this with your splash screen UI
      ),
    );
  }
}
```





