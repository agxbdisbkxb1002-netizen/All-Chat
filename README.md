محمد:
class StoryModel{

String uid;

String image;

DateTime time;

StoryModel({

required this.uid,

required this.image,

required this.time,

});

}

Future uploadStory(

String image,

String uid,

)async{

await firestore.collection(

"stories",

).add({

"uid":uid,

"image":image,

"time":Timestamp.now(),

});

}

SizedBox(

height:100,

child: ListView.builder(

scrollDirection: Axis.horizontal,

itemCount:10,

itemBuilder:(_,i){

return const Padding(

padding: EdgeInsets.all(8),

child: CircleAvatar(

radius:35,

),

);

},

),

)

Future sendLocation(

double lat,

double lng,

)async{

await firestore.collection(

"messages",

).add({

"type":"location",

"lat":lat,

"lng":lng,

"time":Timestamp.now(),

});

}

launchUrl(

Uri.parse(

"https://maps.google.com/?q=$lat,$lng",

),

);

bool pinned;

Future pin(

String id,

bool value,

)async{

await firestore.collection(

"messages",

).doc(id).update({

"pinned":value,

});

}

Future deleteForEveryone(

String id,

)async{

await firestore.collection(

"messages",

).doc(id).update({

"deleted":true,

"text":"این پیام حذف شد",

});

}

Text(

deleted

? "این پیام حذف شده است"

: text,

)

bool appLock=true;

class AppTheme{

Color primary;

Color background;

Color bubble;

AppTheme({

required this.primary,

required this.background,

required this.bubble,

});

}

class FriendRequest {

  final String id;
  final String from;
  final String to;
  final bool accepted;

  FriendRequest({
    required this.id,
    required this.from,
    required this.to,
    required this.accepted,
  });

  Map<String,dynamic> toMap(){
    return{
      "id":id,
      "from":from,
      "to":to,
      "accepted":accepted,
    };
  }

}

import 'package:cloud_firestore/cloud_firestore.dart';

class FriendService{

final FirebaseFirestore firestore=
FirebaseFirestore.instance;

Future sendRequest(

String from,
String to,

)async{

await firestore.collection("requests").add({

"from":from,
"to":to,
"accepted":false,
"time":Timestamp.now(),

});

}

Future accept(String id)async{

await firestore.collection("requests").doc(id).update({

"accepted":true,

});

}

}

Future blockUser(

String myUid,
String userUid,

)async{

await firestore.collection("blocks").add({

"owner":myUid,
"blocked":userUid,

});

}

Future unblock(

String documentId,

)async{

await firestore.collection("blocks")

.doc(documentId)

.delete();

}

Future reportUser(

String uid,
String reason,

)async{

await firestore.collection("reports").add({

"user":uid,
"reason":reason,
"time":Timestamp.now(),

});

}

bool muted;

Future mute(

String roomId,
bool value,

)async{

await firestore.collection("rooms")

.doc(roomId)

.update({

"muted":value,

});

}

Future sendDisappear(

String text,

)async{

await firestore.collection("messages").add({

"text":text,

"expire":

Timestamp.fromDate(

DateTime.now().add(

const Duration(minutes:10),

),

),

});

}

Future deleteExpired() async {

final data=await firestore

.collection("messages")

.get();

for(var doc in data.docs){

final expire=doc["expire"];

if(expire!=null){

if(expire.toDate()

.isBefore(DateTime.now())){

await doc.reference.delete();

}

}

}

}

List search(

List list,

String keyword,

){

return list.where((e){

return e["text"]

.toLowerCase()

.contains(

keyword.toLowerCase(),

);

}).toList();

}

Container(

padding:

const EdgeInsets.all(10),

color: Colors.amber,

child: Row(

children:[

const Icon(Icons.push_pin),

const SizedBox(width:10),

Expanded(

child: Text(

pinnedMessage,

),

)

],

),

)

Future pickProfileImage() async{

final picker=ImagePicker();

final file=

await picker.pickImage(

source: ImageSource.gallery,

);

if(file==null)return;

}

Future saveProfile(

String image,

)async{

await firestore

.collection("users")

.doc(uid)

.update({

"image":image,

});

}

Future logout() async{

await FirebaseAuth.instance.signOut();

}

import 'package:cloud_firestore/cloud_firestore.dart';

class CallService {

  final FirebaseFirestore firestore =
      FirebaseFirestore.instance;

Future startVoiceCall(
      String caller,
      String receiver,
      ) async {

    await firestore.collection("calls").add({

      "caller": caller,
      "receiver": receiver,
      "type": "voice",
      "status": "ringing",
      "time": Timestamp.now(),

    });

  }

  Future endCall(String id) async {

    await firestore
        .collection("calls")
        .doc(id)
        .update({

      "status": "ended",

    });

  }

}

Future startVideoCall(

String caller,

String receiver,

) async {

await firestore.collection("calls").add({

"caller":caller,

"receiver":receiver,

"type":"video",

"status":"ringing",

"time":Timestamp.now(),

});

}

class CallModel{

String id;

String caller;

String receiver;

String type;

String status;

DateTime time;

CallModel({

required this.id,

required this.caller,

required this.receiver,

required this.type,

required this.status,

required this.time,

});

}

import 'package:flutter/material.dart';

class CallScreen extends StatelessWidget{

const CallScreen({super.key});

@override
Widget build(BuildContext context){

return Scaffold(

backgroundColor: Colors.black,

body: Center(

child: Column(

mainAxisAlignment:
MainAxisAlignment.center,

children:[

const CircleAvatar(

radius:60,

child: Icon(Icons.person,size:60),

),

const SizedBox(height:20),

const Text(

"در حال تماس...",

style: TextStyle(

color: Colors.white,

fontSize:22,

),

),

const SizedBox(height:30),

FloatingActionButton(

backgroundColor: Colors.red,

onPressed: (){

Navigator.pop(context);

},

child: const Icon(Icons.call_end),

)

],

),

),

);

}

}

List<String> emojis=[

"😀",
"😂",
"😍",
"❤️",
"👍",
"🔥",
"😭",
"😎",

];

IconButton(

icon: const Icon(Icons.emoji_emotions),

onPressed: (){

controller.text+="😀";

},

)

Future saveTheme(

int index,

) async{

final pref=

await SharedPreferences.getInstance();

await pref.setInt(

"theme",

index,

);

}

Future<int> loadTheme() async{

final pref=

await SharedPreferences.getInstance();

return pref.getInt("theme")??0;

}

Future updateGroupImage(

String groupId,

String image,

) async{

await firestore.collection(

"groups",

).doc(groupId).update({

"image":image,

});

}

Future deleteGroup(

String id,

) async{

await firestore.collection(

"groups",

).doc(id).delete();

}

Future leaveGroup(

String id,

String uid,

List members,

) async{

members.remove(uid);

await firestore.collection(

"groups",

).doc(id).update({

"members":members,

});

}

Future archive(

String room,

) async{

await firestore.collection(

"rooms",

).doc(room).update({

"archive":true,

});

}

Future unArchive(

String room,

) async{

await firestore.collection(

"rooms",

).doc(room).update({

"archive":false,

});

}

import 'package:cloud_firestore/cloud_firestore.dart';

class PresenceService {

  final FirebaseFirestore firestore =
      FirebaseFirestore.instance;

  Future setStatus(
      String uid,
      bool online,
      ) async {

    await firestore.collection("users").doc(uid).update({

      "online": online,
      "lastSeen": Timestamp.now(),

    });

  }

}

import 'package:flutter/material.dart';

class UserStatus extends StatelessWidget {

  final bool online;

  final String lastSeen;

  const UserStatus({
    super.key,
    required this.online,
    required this.lastSeen,
  });

  @override
  Widget build(BuildContext context) {

    return Row(

      children: [

        CircleAvatar(
          radius:5,
          backgroundColor:
          online
              ? Colors.green
              : Colors.grey,
        ),

        const SizedBox(width:6),

        Text(

          online
              ? "آنلاین"
              : lastSeen,

        )

      ],

    );

  }

}

import 'package:shared_preferences/shared_preferences.dart';

class ThemeService {

  Future save(int color) async {

    final pref =
        await SharedPreferences.getInstance();

    await pref.setInt("themeColor", color);

  }

  Future<int> load() async {

    final pref =
        await SharedPreferences.getInstance();

return pref.getInt("themeColor") ?? 0;

  }

}

class Routes {

  static const login="/login";
  static const register="/register";
  static const home="/home";
  static const users="/users";
  static const chat="/chat";
  static const profile="/profile";
  static const settings="/settings";
  static const themes="/themes";

}

class Validators {
  static String? email(String? value) {
    if (value == null || value.isEmpty) {
      return "ایمیل را وارد کنید";
    }

    if (!value.contains("@")) {
      return "ایمیل معتبر نیست";
    }

    return null;
  }

  static String? password(String? value) {
    if (value == null || value.length < 6) {
      return "رمز عبور باید حداقل 6 کاراکتر باشد";
    }

    return null;
  }

  static String? username(String? value) {
    if (value == null || value.trim().length < 3) {
      return "نام کاربری کوتاه است";
    }

    return null;
  }
}

import 'package:shared_preferences/shared_preferences.dart';

class CacheService {

  Future saveUid(String uid) async {

    final pref =
        await SharedPreferences.getInstance();

    await pref.setString("uid", uid);

  }

  Future<String?> getUid() async {

    final pref =
        await SharedPreferences.getInstance();

    return pref.getString("uid");

  }

  Future logout() async {

    final pref =
        await SharedPreferences.getInstance();

    await pref.clear();

  }

}

import 'package:flutter/material.dart';

class Loading extends StatelessWidget {

  const Loading({super.key});

  @override
  Widget build(BuildContext context) {

    return const Center(

      child: CircularProgressIndicator(),

    );

  }

}

import 'package:flutter/material.dart';

class PrimaryButton extends StatelessWidget {

  final String text;

  final VoidCallback onPressed;

  const PrimaryButton({

    super.key,

    required this.text,

    required this.onPressed,

  });

  @override
  Widget build(BuildContext context) {

    return SizedBox(

      width: double.infinity,

      height: 55,

      child: ElevatedButton(

        onPressed: onPressed,

        child: Text(text),

      ),

    );

  }

}

import 'package:flutter/material.dart';

class CustomTextField extends StatelessWidget {

  final TextEditingController controller;

  final String hint;

  final bool password;

  const CustomTextField({

    super.key,

    required this.controller,

    required this.hint,

    this.password=false,

  });

  @override
  Widget build(BuildContext context){

    return TextField(

      controller: controller,

      obscureText: password,

      decoration: InputDecoration(

        hintText: hint,

        border: OutlineInputBorder(

          borderRadius: BorderRadius.circular(15),

        ),

      ),

    );

  }

}

import 'package:flutter/material.dart';

class AppColors {

  static const primary=Color(0xff6750A4);

  static const success=Colors.green;

  static const error=Colors.red;

  static const background=Color(0xffF5F5F5);

}

class AppConstants {

  static const appName="Messenger";

  static const version="1.0.0";

  static const messageLimit=1000;

  static const imageLimit=5;

}

include: package:flutter_lints/flutter.yaml

linter:
  rules:
    prefer_const_constructors: true
    avoid_print: true

محمد:
name: chat_app
description: Flutter Chat App
publish_to: "none"

version: 1.0.0+1

environment:
  sdk: ">=3.3.0 <4.0.0"

dependencies:
  flutter:
    sdk: flutter

  cupertino_icons: ^1.0.8

  firebase_core: ^3.6.0
  firebase_auth: ^5.3.1
  cloud_firestore: ^5.4.4

  provider: ^6.1.2

  shared_preferences: ^2.3.2

  google_fonts: ^6.2.1

  flutter_colorpicker: ^1.1.0

dev_dependencies:
  flutter_test:
    sdk: flutter

  flutter_lints: ^4.0.0

flutter:
  uses-material-design: true

import 'package:flutter/material.dart';

void main() {
  runApp(const ChatApp());
}

class ChatApp extends StatelessWidget {
  const ChatApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: "Chat App",
      theme: ThemeData(
        colorSchemeSeed: Colors.deepPurple,
        useMaterial3: true,
      ),
      home: const HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  const HomePage({super.key});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {

  final TextEditingController controller = TextEditingController();

  List<String> messages = [];

  void sendMessage() {
    if(controller.text.trim().isEmpty) return;

    setState(() {
      messages.add(controller.text);
      controller.clear();
    });
  }

  void deleteMessage(int index){
    setState(() {
      messages.removeAt(index);
    });
  }

  void editMessage(int index){

    controller.text = messages[index];

    showDialog(
      context: context,
      builder: (_) {
        return AlertDialog(
          title: const Text("ویرایش پیام"),
          content: TextField(
            controller: controller,
          ),
          actions: [
            TextButton(
              onPressed: (){
                Navigator.pop(context);
              },
              child: const Text("لغو"),
            ),
            ElevatedButton(
              onPressed: (){
                setState(() {
                  messages[index]=controller.text;
                  controller.clear();
                });
                Navigator.pop(context);
              },
              child: const Text("ذخیره"),
            ),
          ],
        );
      },
    );
  }

  @override
  Widget build(BuildContext context){

    return Scaffold(

      appBar: AppBar(
        title: const Text("Messenger"),
      ),

      body: Column(

        children: [

          Expanded(

            child: ListView.builder(

              itemCount: messages.length,

              itemBuilder: (_,index){

                return Card(

                  child: ListTile(

                    title: Text(messages[index]),

                    trailing: Row(
                      mainAxisSize: MainAxisSize.min,
                      children: [

                        IconButton(

                          icon: const Icon(Icons.edit),

                          onPressed: (){
                            editMessage(index);
                          },

                        ),

                        IconButton(

                          icon: const Icon(Icons.delete),

                          onPressed: (){
                            deleteMessage(index);
                          },

                        )

                      ],
                    ),

                  ),

                );

              },

            ),

          ),

          Padding(

            padding: const EdgeInsets.all(10),

            child: Row(

              children: [

                Expanded(

                  child: TextField(

                    controller: controller,

                    decoration: const InputDecoration(

                      hintText: "پیام بنویس...",

                      border: OutlineInputBorder(),

                    ),

                  ),

                ),

                const SizedBox(width:10),

                FloatingActionButton(

                  onPressed: sendMessage,

child: const Icon(Icons.send),

                )

              ],

            ),

          )

        ],

      ),

    );

  }

}

class MessageModel {
  final String id;
  final String senderId;
  final String receiverId;
  final String text;
  final DateTime time;

  MessageModel({
    required this.id,
    required this.senderId,
    required this.receiverId,
    required this.text,
    required this.time,
  });

  Map<String, dynamic> toMap() {
    return {
      "id": id,
      "senderId": senderId,
      "receiverId": receiverId,
      "text": text,
      "time": time.millisecondsSinceEpoch,
    };
  }

  factory MessageModel.fromMap(Map<String, dynamic> map) {
    return MessageModel(
      id: map["id"],
      senderId: map["senderId"],
      receiverId: map["receiverId"],
      text: map["text"],
      time: DateTime.fromMillisecondsSinceEpoch(map["time"]),
    );
  }
}

import 'package:flutter/material.dart';

class LoginScreen extends StatefulWidget {
  const LoginScreen({super.key});

  @override
  State<LoginScreen> createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {

  final email = TextEditingController();
  final password = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Padding(
          padding: const EdgeInsets.all(20),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [

              const Icon(Icons.chat,size:90),

              const SizedBox(height:20),

              TextField(
                controller: email,
                decoration: const InputDecoration(
                  labelText: "ایمیل",
                  border: OutlineInputBorder(),
                ),
              ),

              const SizedBox(height:15),

              TextField(
                controller: password,
                obscureText: true,
                decoration: const InputDecoration(
                  labelText: "رمز عبور",
                  border: OutlineInputBorder(),
                ),
              ),

              const SizedBox(height:20),

              SizedBox(
                width: double.infinity,
                height: 50,
                child: ElevatedButton(
                  onPressed: () {},
                  child: const Text("ورود"),
                ),
              ),

              TextButton(
                onPressed: () {},
                child: const Text("ثبت نام"),
              )

            ],
          ),
        ),
      ),
    );
  }
}

class UserModel {

  final String uid;
  final String name;
  final String email;
  final String image;

  UserModel({
    required this.uid,
    required this.name,
    required this.email,
    required this.image,
  });

  Map<String,dynamic> toMap(){
    return{
      "uid":uid,
      "name":name,
      "email":email,
      "image":image,
    };
  }

  factory UserModel.fromMap(Map<String,dynamic> map){
    return UserModel(
      uid: map["uid"],
      name: map["name"],
      email: map["email"],
      image: map["image"],
    );
  }
}

import 'package:flutter/material.dart';

class RegisterScreen extends StatefulWidget {
  const RegisterScreen({super.key});

  @override
  State<RegisterScreen> createState() => _RegisterScreenState();
}

class _RegisterScreenState extends State<RegisterScreen> {

  final name = TextEditingController();
  final email = TextEditingController();
  final password = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("ثبت نام")),
      body: Padding(
        padding: const EdgeInsets.all(20),
        child: Column(
          children: [

            TextField(
              controller: name,
              decoration: const InputDecoration(
                labelText: "نام",
              ),
            ),

            const SizedBox(height:15),

TextField(
              controller: email,
              decoration: const InputDecoration(
                labelText: "ایمیل",
              ),
            ),

            const SizedBox(height:15),

            TextField(
              controller: password,
              obscureText: true,
              decoration: const InputDecoration(
                labelText: "رمز عبور",
              ),
            ),

            const SizedBox(height:30),

            SizedBox(
              width: double.infinity,
              child: ElevatedButton(
                onPressed: () {},
                child: const Text("ثبت نام"),
              ),
            )

          ],
        ),
      ),
    );
  }
}

import 'package:flutter/material.dart';

class MessageBubble extends StatelessWidget {

  final String text;
  final bool isMe;
  final VoidCallback onDelete;
  final VoidCallback onEdit;

  const MessageBubble({
    super.key,
    required this.text,
    required this.isMe,
    required this.onDelete,
    required this.onEdit,
  });

  @override
  Widget build(BuildContext context) {

    return Align(

      alignment:
          isMe ? Alignment.centerRight : Alignment.centerLeft,

      child: GestureDetector(

        onLongPress: () {

          showModalBottomSheet(
            context: context,
            builder: (_) {
              return Column(
                mainAxisSize: MainAxisSize.min,
                children: [

                  ListTile(
                    leading: const Icon(Icons.edit),
                    title: const Text("ویرایش"),
                    onTap: onEdit,
                  ),

                  ListTile(
                    leading: const Icon(Icons.delete),
                    title: const Text("حذف"),
                    onTap: onDelete,
                  ),

                ],
              );
            },
          );
        },

        child: Container(

          margin: const EdgeInsets.all(8),

          padding: const EdgeInsets.all(14),

          decoration: BoxDecoration(

            color: isMe
                ? Colors.blue
                : Colors.grey.shade300,

            borderRadius: BorderRadius.circular(18),

          ),

          child: Text(
            text,
            style: TextStyle(
              color: isMe
                  ? Colors.white
                  : Colors.black,
            ),
          ),

        ),

      ),

    );

  }
}

import 'package:flutter/material.dart';

class ThemeProvider extends ChangeNotifier {

  bool dark = false;

  int currentColor = 0;

  List<Color> colors = [

    Colors.red,
    Colors.pink,
    Colors.purple,
    Colors.deepPurple,
    Colors.indigo,
    Colors.blue,
    Colors.lightBlue,
    Colors.cyan,
    Colors.teal,
    Colors.green,
    Colors.lightGreen,
    Colors.lime,
    Colors.yellow,
    Colors.amber,
    Colors.orange,
    Colors.deepOrange,
    Colors.brown,
    Colors.grey,
    Colors.blueGrey,
    Colors.black,
    Colors.redAccent,
    Colors.greenAccent,
    Colors.cyanAccent,
    Colors.deepPurpleAccent,

  ];

  void changeColor(int index){

    currentColor=index;

    notifyListeners();

  }

  void changeTheme(){

    dark=!dark;

    notifyListeners();

  }

}

import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../providers/theme_provider.dart';

class ThemeScreen extends StatelessWidget {

  const ThemeScreen({super.key});

  @override
  Widget build(BuildContext context) {

    final provider=Provider.of<ThemeProvider>(context);

    return Scaffold(

      appBar: AppBar(
        title: const Text("تم برنامه"),
      ),

      body: GridView.builder(

        padding: const EdgeInsets.all(20),

        gridDelegate:
            const SliverGridDelegateWithFixedCrossAxisCount(
          crossAxisCount: 4,
          crossAxisSpacing: 15,
          mainAxisSpacing: 15,
        ),

        itemCount: provider.colors.length,

        itemBuilder: (_,index){

          return InkWell(

onTap: (){
              provider.changeColor(index);
            },

            child: CircleAvatar(

              backgroundColor:
                  provider.colors[index],

            ),

          );

        },

      ),

      floatingActionButton: FloatingActionButton(

        onPressed: (){
          provider.changeTheme();
        },

        child: const Icon(Icons.dark_mode),

      ),

    );

  }
}

import 'package:flutter/material.dart';
import 'chat_screen.dart';

class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  final List<Map<String, String>> users = const [
    {
      "name": "Ali",
      "email": "ali@gmail.com",
    },
    {
      "name": "Sara",
      "email": "sara@gmail.com",
    },
    {
      "name": "Reza",
      "email": "reza@gmail.com",
    },
    {
      "name": "Amir",
      "email": "amir@gmail.com",
    },
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Messenger"),
      ),
      body: ListView.builder(
        itemCount: users.length,
        itemBuilder: (_, index) {
          return ListTile(
            leading: const CircleAvatar(
              child: Icon(Icons.person),
            ),
            title: Text(users[index]["name"]!),
            subtitle: Text(users[index]["email"]!),
            trailing: const Icon(Icons.chat),
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (_) => ChatScreen(
                    username: users[index]["name"]!,
                  ),
                ),
              );
            },
          );
        },
      ),
    );
  }
}

import 'package:flutter/material.dart';
import '../widgets/message_bubble.dart';

class ChatScreen extends StatefulWidget {
  final String username;

  const ChatScreen({
    super.key,
    required this.username,
  });

  @override
  State<ChatScreen> createState() => _ChatScreenState();
}

class _ChatScreenState extends State<ChatScreen> {

  List<String> messages = [];

  final controller = TextEditingController();

  void send() {
    if (controller.text.trim().isEmpty) return;

    setState(() {
      messages.add(controller.text);
    });

    controller.clear();
  }

  @override
  Widget build(BuildContext context) {

    return Scaffold(

      appBar: AppBar(
        title: Text(widget.username),
      ),

      body: Column(

        children: [

          Expanded(

            child: ListView.builder(

              itemCount: messages.length,

              itemBuilder: (_, index) {

                return MessageBubble(

                  text: messages[index],

                  isMe: true,

                  onDelete: () {

                    setState(() {
                      messages.removeAt(index);
                    });

                  },

                  onEdit: () {},

                );

              },

            ),

          ),

          Padding(

            padding: const EdgeInsets.all(10),

            child: Row(

              children: [

                Expanded(

                  child: TextField(

                    controller: controller,

                    decoration: const InputDecoration(
                      hintText: "پیام...",
                    ),

                  ),

                ),

                IconButton(

                  onPressed: send,

                  icon: const Icon(Icons.send),

                )

              ],

            ),

          )

        ],

      ),

    );

  }

}

import 'package:firebase_auth/firebase_auth.dart';

class AuthService {

  final FirebaseAuth auth = FirebaseAuth.instance;

  Future<UserCredential> login(
      String email,
      String password,
      ) {

    return auth.signInWithEmailAndPassword(
      email: email,
      password: password,
    );

  }

  Future<UserCredential> register(

      String email,

      String password,

      ) {

return auth.createUserWithEmailAndPassword(
      email: email,
      password: password,
    );

  }

  Future logout() {

    return auth.signOut();

  }

}

import 'package:cloud_firestore/cloud_firestore.dart';

class ChatService {

  final FirebaseFirestore firestore =
      FirebaseFirestore.instance;

  Future sendMessage(

      String sender,

      String receiver,

      String text,

      ) {

    return firestore.collection("messages").add({

      "sender": sender,

      "receiver": receiver,

      "text": text,

      "time": Timestamp.now(),

    });

  }

}

import 'package:cloud_firestore/cloud_firestore.dart';

class ChatService {
  final FirebaseFirestore firestore = FirebaseFirestore.instance;

  Stream<QuerySnapshot> getMessages(
      String sender,
      String receiver,
      ) {
    return firestore
        .collection("messages")
        .orderBy("time")
        .snapshots();
  }

  Future sendMessage(
      String sender,
      String receiver,
      String text,
      ) async {

    await firestore.collection("messages").add({

      "sender": sender,

      "receiver": receiver,

      "text": text,

      "time": Timestamp.now(),

    });

  }

  Future deleteMessage(String id) async {

    await firestore.collection("messages").doc(id).delete();

  }

  Future editMessage(

      String id,

      String newText,

      ) async {

    await firestore.collection("messages").doc(id).update({

      "text": newText,

    });

  }

}

import 'package:flutter/material.dart';

class ChatTextField extends StatelessWidget {

  final TextEditingController controller;

  final VoidCallback send;

  const ChatTextField({

    super.key,

    required this.controller,

    required this.send,

  });

  @override
  Widget build(BuildContext context) {

    return Container(

      padding: const EdgeInsets.all(8),

      child: Row(

        children: [

          Expanded(

            child: TextField(

              controller: controller,

              decoration: InputDecoration(

                hintText: "پیام...",

                border: OutlineInputBorder(

                  borderRadius:
                  BorderRadius.circular(30),

                ),

              ),

            ),

          ),

          const SizedBox(width:8),

          FloatingActionButton.small(

            onPressed: send,

            child: const Icon(Icons.send),

          )

        ],

      ),

    );

  }

}

Expanded(

 child: StreamBuilder(

 stream: ChatService().getMessages(

 "1",

 "2",

 ),

 builder: (context,snapshot){

 if(!snapshot.hasData){

 return const Center(

 child:CircularProgressIndicator(),

 );

 }

 final docs=snapshot.data!.docs;

 return ListView.builder(

 itemCount: docs.length,

 itemBuilder:(context,index){

 final data=docs[index];

 return MessageBubble(

 text:data["text"],

 isMe:data["sender"]=="1",

 onDelete:(){

 ChatService().deleteMessage(

 data.id,

 );

 },

 onEdit:(){

},

 );

 },

 );

 },

 ),

),

void editDialog(String id,String oldText){

controller.text=oldText;

showDialog(

context: context,

builder: (_){

return AlertDialog(

title: const Text("ویرایش پیام"),

content: TextField(

controller: controller,

),

actions:[

ElevatedButton(

onPressed:(){

ChatService().editMessage(

id,

controller.text,

);

Navigator.pop(context);

},

child: const Text("ذخیره"),

)

],

);

}

);

}

import 'package:flutter/material.dart';

class ProfileAvatar extends StatelessWidget{

final String name;

const ProfileAvatar({

super.key,

required this.name,

});

@override
Widget build(BuildContext context){

return CircleAvatar(

radius:25,

child: Text(

name.substring(0,1),

style: const TextStyle(

fontSize:20,

),

),

);

}

}

import 'package:flutter/material.dart';

class ProfileScreen extends StatelessWidget{

const ProfileScreen({super.key});

@override
Widget build(BuildContext context){

return Scaffold(

appBar: AppBar(

title: const Text("پروفایل"),

),

body: Column(

children:[

const SizedBox(height:30),

const CircleAvatar(

radius:50,

child: Icon(Icons.person,size:60),

),

const SizedBox(height:20),

const Text(

"نام کاربر",

style: TextStyle(

fontSize:22,

),

),

const Text(

"email@gmail.com",

),

const SizedBox(height:40),

ListTile(

leading: const Icon(Icons.color_lens),

title: const Text("تم برنامه"),

onTap: (){

},

),

ListTile(

leading: const Icon(Icons.logout),

title: const Text("خروج"),

onTap: (){

},

),

],

),

);

}

}

import 'package:cloud_firestore/cloud_firestore.dart';

class UserService {

  final FirebaseFirestore firestore =
      FirebaseFirestore.instance;

  Stream<QuerySnapshot> getUsers() {

    return firestore
        .collection("users")
        .snapshots();

  }

  Future setOnline(String uid) async {

    await firestore
        .collection("users")
        .doc(uid)
        .update({

      "online": true,

      "lastSeen": Timestamp.now(),

    });

  }

  Future setOffline(String uid) async {

    await firestore
        .collection("users")
        .doc(uid)
        .update({

      "online": false,

      "lastSeen": Timestamp.now(),

    });

  }

}

import 'package:flutter/material.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import '../services/user_service.dart';
import 'chat_screen.dart';

class UsersScreen extends StatelessWidget {

  const UsersScreen({super.key});

  @override
  Widget build(BuildContext context) {

    return Scaffold(

      appBar: AppBar(
        title: const Text("کاربران"),
      ),

      body: StreamBuilder<QuerySnapshot>(

        stream: UserService().getUsers(),

        builder: (context, snapshot) {

          if (!snapshot.hasData) {

            return const Center(
              child: CircularProgressIndicator(),
            );

          }

          final users = snapshot.data!.docs;

          return ListView.builder(

            itemCount: users.length,

            itemBuilder: (_, index) {

              final data = users[index];

              return ListTile(

                leading: CircleAvatar(

                  child: Text(
                    data["name"][0],
                  ),

                ),

                title: Text(data["name"]),

                subtitle: Text(

                  data["online"]

                      ? "آنلاین"

                      : "آخرین بازدید: ${data["lastSeen"]}",

                ),

                trailing: const Icon(Icons.chat),

                onTap: () {

                  Navigator.push(

                    context,

                    MaterialPageRoute(

                      builder: (_) => ChatScreen(

                        username: data["name"],

                      ),

                    ),

                  );

                },

              );

            },

          );

        },

      ),

    );

  }

}

import 'package:flutter/material.dart';

class SettingsScreen extends StatelessWidget {

  const SettingsScreen({super.key});

  @override
  Widget build(BuildContext context) {

    return Scaffold(

      appBar: AppBar(

        title: const Text("تنظیمات"),

      ),

      body: ListView(

        children: [

          SwitchListTile(

            value: true,

            onChanged: (v) {},

            title: const Text("حالت تاریک"),

          ),

          ListTile(

            leading: const Icon(Icons.color_lens),

            title: const Text("رنگ برنامه"),

            onTap: () {},

          ),

          ListTile(

            leading: const Icon(Icons.notifications),

            title: const Text("اعلان ها"),

            onTap: () {},

          ),

          ListTile(

            leading: const Icon(Icons.lock),

            title: const Text("حریم خصوصی"),

            onTap: () {},

          ),

          ListTile(

            leading: const Icon(Icons.logout),

            title: const Text("خروج"),

            onTap: () {},

          ),

        ],

      ),

    );

  }

}

import 'package:flutter/material.dart';

class SearchBox extends StatelessWidget {

  final TextEditingController controller;

final Function(String) onChanged;

  const SearchBox({

    super.key,

    required this.controller,

    required this.onChanged,

  });

  @override
  Widget build(BuildContext context) {

    return Padding(

      padding: const EdgeInsets.all(12),

      child: TextField(

        controller: controller,

        onChanged: onChanged,

        decoration: InputDecoration(

          hintText: "جستجوی کاربر...",

          prefixIcon: const Icon(Icons.search),

          border: OutlineInputBorder(

            borderRadius: BorderRadius.circular(30),

          ),

        ),

      ),

    );

  }

}

image_picker: ^1.1.2
firebase_storage: ^12.3.3
flutter_sound: ^9.2.13
path_provider: ^2.1.5

import 'dart:io';
import 'package:firebase_storage/firebase_storage.dart';

class StorageService {

  final FirebaseStorage storage =
      FirebaseStorage.instance;

  Future<String> uploadImage(File file) async {

    final ref = storage.ref().child(
      "images/${DateTime.now().millisecondsSinceEpoch}.jpg",
    );

    await ref.putFile(file);

    return await ref.getDownloadURL();

  }

}

import 'package:flutter/material.dart';

class ImageMessage extends StatelessWidget {

  final String url;

  const ImageMessage({

    super.key,

    required this.url,

  });

  @override
  Widget build(BuildContext context){

    return Padding(

      padding: const EdgeInsets.all(8),

      child: ClipRRect(

        borderRadius: BorderRadius.circular(15),

        child: Image.network(

          url,

          width:220,

        ),

      ),

    );

  }

}

final picker = ImagePicker();

Future pickImage() async {

final file = await picker.pickImage(

source: ImageSource.gallery,

);

if(file==null)return;

}

import 'package:flutter/material.dart';

class BottomNav extends StatelessWidget {

  final int index;

  final Function(int) onTap;

  const BottomNav({

    super.key,

    required this.index,

    required this.onTap,

  });

  @override
  Widget build(BuildContext context){

    return NavigationBar(

      selectedIndex:index,

      onDestinationSelected:onTap,

      destinations: const[

        NavigationDestination(

          icon: Icon(Icons.chat),

          label:"چت",

        ),

        NavigationDestination(

          icon: Icon(Icons.people),

          label:"کاربران",

        ),

        NavigationDestination(

          icon: Icon(Icons.person),

          label:"پروفایل",

        ),

        NavigationDestination(

          icon: Icon(Icons.settings),

          label:"تنظیمات",

        ),

      ],

    );

  }

}

import 'package:flutter/material.dart';

class EditProfile extends StatefulWidget{

const EditProfile({super.key});

@override
State<EditProfile> createState()=>_EditProfileState();

}

class _EditProfileState extends State<EditProfile>{

final name=TextEditingController();

final bio=TextEditingController();

@override
Widget build(BuildContext context){

return Scaffold(

appBar: AppBar(

title: const Text("ویرایش پروفایل"),

),

body: Padding(

padding: const EdgeInsets.all(20),

child: Column(

children:[

const CircleAvatar(

radius:45,

child: Icon(Icons.person),

),

const SizedBox(height:20),

TextField(

controller:name,

decoration: const InputDecoration(

labelText:"نام",

),

),

const SizedBox(height:15),

TextField(

controller:bio,

decoration: const InputDecoration(

labelText:"بیوگرافی",

),

),

const SizedBox(height:25),

ElevatedButton(

onPressed:(){},

child: const Text("ذخیره"),

)

],

),

),

);

}

}

class MessageModel {

String id;

String sender;

String receiver;

String message;

String type;

DateTime time;

MessageModel({

required this.id,

required this.sender,

required this.receiver,

required this.message,

required this.type,

required this.time,

});

}

import 'package:flutter_local_notifications/flutter_local_notifications.dart';

class NotificationService {

  static final FlutterLocalNotificationsPlugin notifications =
      FlutterLocalNotificationsPlugin();

  static Future init() async {

const android = AndroidInitializationSettings(
      '@mipmap/ic_launcher',
    );

    const settings = InitializationSettings(
      android: android,
    );

    await notifications.initialize(settings);

  }

  static Future show(String title, String body) async {

    const android = AndroidNotificationDetails(
      "chat",
      "Messages",
      importance: Importance.max,
      priority: Priority.high,
    );

    await notifications.show(
      0,
      title,
      body,
      const NotificationDetails(
        android: android,
      ),
    );

  }

}

class ChatRoom {

  final String id;

  final List<String> users;

  final String lastMessage;

  final DateTime lastTime;

  ChatRoom({

    required this.id,

    required this.users,

    required this.lastMessage,

    required this.lastTime,

  });

}

import 'package:flutter/material.dart';

class ChatListScreen extends StatelessWidget {

  const ChatListScreen({super.key});

  @override
  Widget build(BuildContext context){

    return Scaffold(

      appBar: AppBar(
        title: const Text("گفتگوها"),
      ),

      body: ListView(

        children: const [

          ListTile(

            leading: CircleAvatar(),

            title: Text("Ali"),

            subtitle: Text("سلام خوبی؟"),

            trailing: Text("18:42"),

          ),

          ListTile(

            leading: CircleAvatar(),

            title: Text("Sara"),

            subtitle: Text("باشه"),

            trailing: Text("12:10"),

          ),

        ],

      ),

    );

  }

}

bool seen;
bool edited;
bool deleted;

required this.seen,
required this.edited,
required this.deleted,

Future seenMessage(String id) async {

await firestore

.collection("messages")

.doc(id)

.update({

"seen":true,

});

}

Row(

mainAxisSize: MainAxisSize.min,

children:[

Text(message),

const SizedBox(width:5),

Icon(

seen

? Icons.done_all

: Icons.done,

size:16,

color: Colors.blue,

),

],

)

typing : true
typingTo : uid

Future typing(

String uid,

bool value,

) async{

await firestore

.collection("users")

.doc(uid)

.update({

"typing":value,

});

}

Text(

userTyping

? "در حال تایپ..."

: "آنلاین",

)

String replyMessage;
String replyUser;

Container(

padding: const EdgeInsets.all(10),

color: Colors.grey.shade300,

child: Column(

crossAxisAlignment:
CrossAxisAlignment.start,

children:[

Text(replyUser),

Text(replyMessage),

],

),

)

Future forward(

String text,

String receiver,

) async{

await firestore.collection(

"messages",

).add({

"text":text,

"receiver":receiver,

"time":Timestamp.now(),

});

}

PopupMenuButton(

itemBuilder:(_)=>[

const PopupMenuItem(

value:1,

child: Text("پاسخ"),

),

const PopupMenuItem(

value:2,

child: Text("فوروارد"),

),

const PopupMenuItem(

value:3,

child: Text("ویرایش"),

),

const PopupMenuItem(

value:4,

child: Text("حذف"),

),

],

)

class GroupModel {

  final String id;
  final String name;
  final String image;
  final List<String> members;
  final String admin;

  GroupModel({

    required this.id,
    required this.name,
    required this.image,
    required this.members,
    required this.admin,

  });

  Map<String,dynamic> toMap(){

    return{

      "id":id,
      "name":name,
      "image":image,
      "members":members,
      "admin":admin,

    };

  }

}

import 'package:cloud_firestore/cloud_firestore.dart';

class GroupService{

final FirebaseFirestore firestore=
FirebaseFirestore.instance;

Future createGroup(

String name,

List<String> members,

String admin,

)async{

await firestore.collection("groups").add({

"name":name,

"members":members,

"admin":admin,

"image":"",

"time":Timestamp.now(),

});

}

Stream<QuerySnapshot> groups(){

return firestore.collection("groups").snapshots();

}

}

ElevatedButton(

onPressed: (){

GroupService().createGroup(

"Flutter",

["uid1","uid2"],

"uid1",

);

},

child: const Text("ایجاد گروه"),

)

Future sendFile(

String sender,

String receiver,

String url,

)async{

await firestore.collection("messages").add({

"sender":sender,

"receiver":receiver,

"type":"file",

"file":url,

"time":Timestamp.now(),

});

}

import 'package:url_launcher/url_launcher.dart';

Future download(String url)async{

await launchUrl(

Uri.parse(url),

);

}
