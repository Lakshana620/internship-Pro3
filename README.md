# internship-Pro3
Create a to-do list where you can add, complete and delete tasks. Build the classic to-do app and learn to keep a list of objects in state.
import 'package:flutter/material.dart';

void main() => runApp(MaterialApp(
      theme: ThemeData(colorSchemeSeed: Colors.teal, useMaterial3: true),
      home: const TodoPage(),
    ));

class Todo {
  final String title;
  bool done;
  Todo(this.title, {this.done = false});
}

class TodoPage extends StatefulWidget {
  const TodoPage({super.key});
  @override
  State<TodoPage> createState() => _TodoPageState();
}

class _TodoPageState extends State<TodoPage> {
  final List<Todo> todos = [];
  final controller = TextEditingController();

  void add() {
    final text = controller.text.trim();
    if (text.isEmpty) return;
    setState(() => todos.add(Todo(text)));
    controller.clear();
  }

  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    final remaining = todos.where((t) => !t.done).length;
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Colors.teal,
        foregroundColor: Colors.white,
        title: Text('To-Do (' + remaining.toString() + ' left)'),
      ),
      body: Column(children: [
        Padding(
          padding: const EdgeInsets.all(12),
          child: Row(children: [
            Expanded(
                child: TextField(
                    controller: controller,
                    onSubmitted: (_) => add(),
                    decoration: const InputDecoration(hintText: 'New task'))),
            IconButton(onPressed: add, icon: const Icon(Icons.add)),
          ]),
        ),
        Expanded(
          child: todos.isEmpty
              ? const Center(child: Text('Nothing to do. Add a task!'))
              : ListView.builder(
                  itemCount: todos.length,
                  itemBuilder: (context, i) {
                    final todo = todos[i];
                    return Dismissible(
                      key: ObjectKey(todo),
                      onDismissed: (_) => setState(() => todos.removeAt(i)),
                      background: Container(
                        color: Colors.orange,
                        alignment: Alignment.centerRight,
                        padding: const EdgeInsets.only(right: 16),
                        child: const Icon(Icons.delete, color: Colors.white),
                      ),
                      child: CheckboxListTile(
                        activeColor: Colors.teal,
                        title: Text(todo.title,
                            style: TextStyle(
                                decoration: todo.done
                                    ? TextDecoration.lineThrough
                                    : null)),
                        value: todo.done,
                        onChanged: (v) =>
                            setState(() => todo.done = v ?? false),
                      ),
                    );
                  },
                ),
        ),
      ]),
    );
  }
}
