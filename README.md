基本資料型別：整數、布林
算術運算：+ - * / mod
邏輯運算：and or not
比較運算：> < =
條件判斷：if
變數定義：define
一級函數（first-class function）與函數呼叫
詞彙作用域（Lexical Scope）
環境鍊結（Environment Chaining）

系統架構說明:
1. final.l 詞法分析（Flex）

負責將輸入轉為 token（NUMBER, ID, PLUS, FUN…）

支援關鍵字與符號型 Lisp 語法

2️. final.y 語法分析（Bison）

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

4️. 評估機制（Interpreter 核心）
功能	對應函式
變數查找	find_variable
變數定義	define_variable
加法	TYPE_PLUS + evaluate_exp
比較	calculate_greater / smaller / equal
邏輯	calculate_and / or / not
函數呼叫	evaluate_fun_call
Scope	Environment + parent
