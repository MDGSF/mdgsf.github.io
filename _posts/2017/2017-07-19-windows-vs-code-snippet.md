---
layout: post
title:  "[Windows] VSCode 添加Code Snippets"
date:   2017-07-19
comments: true
categories: Windows
tags: [Windows]
description:
published: true
---

文件 --> 首选项 --> 用户代码片段

然后选择C++

## cpp.json

```
{
/*
     // Place your snippets for C here. Each snippet is defined under a snippet name and has a prefix, body and
     // description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
     // $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the
     // same ids are connected.
     // Example:
     "Print to console": {
        "prefix": "log",,
        "body": [
            "console.log("$1");",
            "$2"
        ],
        "description": "Log output to console"
    }
*/
  "#ifndef … #define … #endif":{
    "prefix": "def",
    "body": "#ifndef ${1:SYMBOL}\n#define $1 ${2:value}\n#endif\t// ${1:SYMBOL}"
  },
  "#include <>":{
    "prefix": "Inc",
    "body": "#include <${1:.h}>"
  },
  "#include \"\"":{
    "prefix": "inc",
    "body": "#include \"${1:.h}\""
  },
  "#pragma mark":{
    "prefix": "mark",
    "body": "#if 0\n${1:#pragma mark -\n}#pragma mark $2\n#endif\n\n$0"
  },
  "main()":{
    "prefix": "main",
    "body": "int main(int argc, char const *argv[]) {\n\t$1\n\treturn 0;\n}"
  },
  "For Loop":{
    "prefix": "for",
    "body": "for (${1:i} = 0; ${1:i} < ${2:count}; ${1:i}${3:++}) {\n\t$4\n}"
  },
  "Define and For Loop":{
    "prefix": "dfor",
    "body": "size_t ${1:i};\nfor (${1:i} = ${2:0}; ${1:i} < ${3:count}; ${1:i}${4:++}) {\n\t$5\n}"
  },

  "Header Include-Guard":{
    "prefix": "once",
    "body": "#ifndef ${1:SYMBOL}\n#define $1\n\n${2}\n\n#endif /* end of include guard: $1 */\n"
  },
  "Typedef":{
    "prefix": "td",
    "body": "typedef ${1:int} ${2:MyCustomType};"
  },
  "Typedef Struct":{
    "prefix": "tst",
    "body": "typedef struct ${1:StructName} {\n\t$2\n}${3:MyCustomType};"
  },
  "Do While Loop":{
    "prefix": "do",
    "body": "do {\n\t$0\n} while($1);"
  },
  "While Loop":{
    "prefix": "while",
    "body": "while ($1) {\n\t$2\n}"
  },
  "fprintf":{
    "prefix": "fprintf",
    "body": "fprintf(${1:stderr}, \"${2:%s}\\\\n\", $3);$4"
  },
  "If Condition":{
    "prefix": "if",
    "body": "if ($1)\n{\n\t$2\n}"
  },
  "If Else":{
    "prefix": "ife",
    "body": "if ($1) {\n\t$2\n} else {\n\t$3\n}"
  },
  "If ElseIf":{
    "prefix": "iff",
    "body": "if ($1) {\n\t$2\n} else if ($3) {\n\t$4\n}"
  },
  "If ElseIf Else":{
    "prefix": "iffe",
    "body": "if ($1) {\n\t$2\n} else if ($3) {\n\t$4\n} else {\n\t$5\n}"
  },
  "Switch Statement":{
    "prefix": "sw",
    "body": "switch ($1) {\n$2default:\n\t${3:break;}\n}$0"
  },
  "case break":{
    "prefix": "cs",
    "body": "case $1:\n{\n\t$2\n}\nbreak;\n$0"
  },
  "printf":{
    "prefix": "printf",
    "body": "printf(\"${1:%s }\\n\", $2);$3"
  },
  "scanf":{
    "prefix": "scanf",
    "body": "scanf(\"${1:%s}\\n\", $2);$3"
  },
  "Struct":{
    "prefix": "st",
    "body": "struct ${1:name_t} {\n\t$2\n};"
  },
  "void":{
    "prefix": "void",
    "body": "void ${1:name}($2) {\n\t$3\n}"
  },
  "any function":{
    "prefix": "func",
    "body": "${1:int} ${2:name}($3) {\n\t$5\n\treturn ${4:0};\n}"
  },
  "write file":{
    "prefix": "wf",
    "body": "FILE *${1:fp};\n${1:fp} = fopen (\"${2:filename.txt}\",\"w\");\nif (${1:fp}!=NULL)\n{\n\tfprintf(${1:fp},\"${3:Some String\\\\n}\");\n\tfclose (${1:fp});\n}"
  },
  "read file":{
    "prefix": "rf",
    "body": "FILE *${1:fp};\n${1:fp} = fopen (\"${2:filename.txt}\",\"r\");\nif (${1:fp}!=NULL)\n{\n\tfscanf(${1:fp},\"${3:Some String\\\\n}\", ${3:&var});\n\tfclose (${1:fp});\n}",
    "description": "read file opeartion including fopen, fscanf and fclose."
  },


  "Enumeration":{
    "prefix": "enum",
    "body": "enum ${1:name} { $0 };"
  },
  "Class":{
    "prefix": "cl",
    "body": "class ${1:name_t} {\nprivate:\n\t${0:/* data */}\n\npublic:\n\t${1:name_t} (${2:arguments});\n\tvirtual ~${1:name_t} ();\n};"
  },
  "Namespace":{
    "prefix": "ns",
    "body": "namespace ${1:name} {\n\t$2\n} /* $1 */"
  },
  "cout":{
    "prefix": "cout",
    "body": "std::cout << \"${1:/* message */}\" << \"\\\\n\";"
  },
  "cin":{
    "prefix": "cin",
    "body": "std::cin >> ${1:/* variable */};"
  },
  "cerr":{
    "prefix": "cerr",
    "body": "std::cerr << \"${1:/* error message */}\" << \"\\\\n\";"
  },
  "std::map":{
    "prefix": "map",
    "body": "std::map<${1:key}, ${2:value}> map$3;"
  },
  "std::string":{
    "prefix": "str",
    "body": "std::string"
  },
  "std::vector":{
    "prefix": "vector",
    "body": "std::vector<${1:int}> v$2;"
  },
  "template <typename>":{
    "prefix": "tp",
    "body": "template <typename ${1:T}>"
  }

}
```
