用 Flex（Lex） 與 Bison（Yacc） 實作一個簡化版 Lisp 直譯器，支援:
基本資料型別：整數、布林
算術運算：+ - * / mod
邏輯運算：and or not
比較運算：> < =
條件判斷：if
變數定義：define
一級函數（first-class function）與函數呼叫
詞彙作用域（Lexical Scope）
環境鍊結（Environment Chaining）

編譯與執行方式:
flex final.l
bison -d final.y
gcc final.tab.c lex.yy.c -o final
./final < test.lisp

系統架構說明:
1. final.l 詞法分析（Flex）
負責將輸入轉為 token（NUMBER, ID, PLUS, FUN…）
支援關鍵字與符號型 Lisp 語法

2. final.y 語法分析（Bison）
建立 AST（以 LispValue 結構表示）
將運算、函數、if 等轉為節點（TYPE_PLUS, TYPE_FUN…）

3. 核心資料結構
typedef struct LispValue {
    LispType type;
    union {
        long num_val;
        bool bool_val;
        char* id_name;
        operands;   // 運算 AST
        fun;        // 函數閉包
    } value;
} LispValue;
typedef struct Environment {
    Variable vars[100];
    int count;
    struct Environment* parent;
} Environment;
Environment 形成鏈結以支援 lexical scope
函數保留定義時的 enclosing environment（closure）

5. Interpreter 核心
evaluate_exp
遞迴計算 AST
處理：
常數
變數查找
算術／邏輯運算
函數呼叫
evaluate_fun_call
建立新的 local environment
將參數綁定到該環境
在函數定義時的環境中執行函數本體

6. 已實作功能對應表
功能	對應函式
變數查找	find_variable
變數定義	define_variable
加法	TYPE_PLUS + evaluate_exp
比較	calculate_greater / smaller / equal
邏輯	calculate_and / or / not
函數呼叫	evaluate_fun_call
Scope	Environment + parent
