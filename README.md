# ICS (Intermediate Compliance Syntax) - Consolidated EBNF Grammar
# Complete Reference

## Design Principles and Separation of Duties

**Context**: ICS is a platform-agnostic intermediate language for compliance checking, designed to serve as a universal format for expressing compliance rules and validation logic. The corpus analysis of 5,419 OVAL definition files revealed diverse platform-specific patterns (Windows registry, Linux files, etc.) that need to be handled without making ICS platform-specific.

**Design Decision**: Clear separation between structural validation and semantic validation.

### **ICS Parser/Validator Responsibilities (This EBNF)**
- **Structural validation**: Block structure, grammar compliance, token format
- **Variable semantics**: Scope validation, reference resolution, type consistency  
- **Universal patterns**: Data flow validation, logical operator correctness
- **Platform-agnostic validation**: Field syntax without platform-specific meaning
- **Reference integrity**: Ensuring variables/objects exist syntactically

### **Compliance Scanner Responsibilities**
- **Platform-specific semantics**: Field appropriateness for criterion types (e.g., `filepath` invalid for `registry` tests)
- **Business logic**: Value ranges, platform compatibility, runtime accessibility
- **Content validation**: Platform-specific syntax (registry hives, file paths, SQL)
- **Runtime verification**: Actual system state checking
- **Value format validation**: Whether registry hive names, file paths are valid

## Core Structure

```ebnf
definition ::= "DEF" variable_declarations? runtime_operations? 
               definition_states? set_operations? criteria "DEF_END"

criteria ::= "CRI" logical_operator negate_flag? criteria_items "CRI_END"
logical_operator ::= "AND" | "OR"
negate_flag ::= "true"

criteria_items ::= (criteria | criterion)+  (* Support nested criteria *)

criterion ::= "CTN" criterion_type test_specification state_references? ctn_states? object_specification "CTN_END"
criterion_type ::= identifier
```

## Variables and Runtime Operations

```ebnf
variable_declarations ::= variable_declaration+
variable_declaration ::= "VAR" variable_name data_type initial_value?
initial_value ::= direct_value
variable_name ::= permanent_variable | temporary_variable
permanent_variable ::= "var_" integer_value
temporary_variable ::= "temp_" integer_value

runtime_operations ::= run_block+
run_block ::= "RUN" variable_name operation_type flat_parameters? "RUN_END"

operation_type ::= "CONCAT" | "SPLIT" | "SUBSTRING" | "`REGEX_CAPTURE`" | "ARITHMETIC" | 
                  "COUNT" | "UNIQUE" | "END" | "MERGE" | "EXTRACT"

flat_parameters ::= flat_parameter+
flat_parameter ::= literal_component | variable_component | object_component | 
                  pattern_spec | delimiter_spec | character_spec | 
                  start_position | length_value | arithmetic_op

(* Core component types - all flat, no nesting *)
literal_component ::= "literal" (backtick_string | raw_string | integer_value)
variable_component ::= "VAR" variable_name
object_component ::= object_reference

(* Object reference types *)
object_reference ::= "OBJ" object_identifier item_field
anonymous_object ::= "OBJ" anonymous_object_spec item_field

object_identifier ::= identifier
item_field ::= identifier

anonymous_object_spec ::= "{" object_field_list "}"
object_field_list ::= object_field+

(* Operation-specific parameters *)
pattern_spec ::= "pattern" (backtick_string | raw_string)
delimiter_spec ::= "delimiter" (backtick_string | raw_string)
character_spec ::= "character" backtick_string
start_position ::= "start" integer_value
length_value ::= "length" integer_value
arithmetic_op ::= "add" | "multiply" | "subtract" | "divide"
```

## Test Specifications

```ebnf
test_specification ::= "TEST" existence_check item_check state_operator?

existence_check ::= "`any exist`" | "`all exist`" | "`none exist`" | 
                   "`at least one exists`" | "`only one exists`"

item_check ::= "all" | "`at least one`" | "`only one`" | "`none satisfy`"

state_operator ::= "AND" | "OR" | "ONE"
```

## State References and Definitions

```ebnf
(* State references for promoted/shared states *)
state_references ::= state_reference+
state_reference ::= "STATE_REF" state_identifier

(* Two-level state placement system *)
definition_states ::= definition_state+
definition_state ::= "STATE" state_identifier state_checks "STATE_END"

ctn_states ::= ctn_state+
ctn_state ::= "STATE" state_identifier state_checks "STATE_END"

state_identifier ::= identifier  (* Generated unique identifier (e.g., ste_20000026) *)

state_checks ::= state_field+ | result_check | empty_state_comment
state_field ::= identifier data_type operation value_spec entity_check?

(* Record datatype support *)
result_check ::= "result" result_validation
result_validation ::= direct_operation | nested_fields | null_check

direct_operation ::= operation value_spec entity_check?
nested_fields ::= result_field+ 
null_check ::= "null" entity_check

result_field ::= "field" field_path data_type operation value_spec entity_check?
field_path ::= backtick_string ("." backtick_string)*

(* Empty state handling *)
empty_state_comment ::= empty_field_default
empty_field_default ::= "trustee_name" "string" "equals" '``'  (* For empty userright states *)

entity_check ::= backtick_string
```

## Object Specifications

```ebnf
object_specification ::= "OBJECT" object_content "OBJECT_END"

object_content ::= set_reference | object_identifier? 
                   (complex_object_elements? object_fields? behavior_spec? filter_spec? empty_object_comment?)

set_reference ::= "SET_REF" variable_name

(* Complex object elements *)
complex_object_elements ::= module_elements? parameters_element? select_element?

module_elements ::= module_element+
module_element ::= module_field backtick_string
module_field ::= "module_name" | "verb" | "noun" | "module_id" | "module_version"

parameters_element ::= "parameters" data_type parameter_fields?
parameter_fields ::= parameter_field+
parameter_field ::= identifier backtick_string

select_element ::= "select" data_type select_fields?
select_fields ::= select_field+
select_field ::= identifier backtick_string

behavior_spec ::= "behavior" behavior_item+
behavior_item ::= identifier | (identifier identifier)  (* attribute value pairs *)

(* Multiple state references in filters *)
filter_spec ::= "FILTER" filter_action? filter_references "FILTER_END"
filter_action ::= "include" | "exclude"
filter_references ::= state_reference+  (* Multiple references supported *)
state_reference ::= "state_ref" state_identifier

object_fields ::= object_field*  (* Allow zero or more fields *)
object_field ::= identifier (backtick_string | raw_string | variable_reference)

(* Empty object handling *)
empty_object_comment ::= "#" "Empty" "object" "-" "no" "fields" "required" "for" "this" "test" "type"
```

## Set Operations

```ebnf
set_operations ::= set_block+
set_block ::= "SET" variable_name set_operation set_operands set_filter_spec? "SET_END"

set_operation ::= union_operation | intersection_operation | complement_operation
union_operation ::= "union"
intersection_operation ::= "intersection"
complement_operation ::= "complement"

set_operands ::= set_operand+ | complement_operands
complement_operands ::= set_operand set_operand  (* Exactly two operands for complement *)

set_operand ::= object_specification | set_reference
set_reference ::= "SET_REF" variable_name

(* SET-level filters with multiple state references *)
set_filter_spec ::= "FILTER" filter_action? set_filter_references "FILTER_END"
set_filter_references ::= state_reference+  (* Multiple state references supported *)
```

## Values and Types

```ebnf
value_spec ::= direct_value | variable_reference

direct_value ::= backtick_string | raw_string | integer_value | boolean_value | multiline_content
variable_reference ::= "VAR" variable_name

(* Record datatype included *)
data_type ::= "string" | "int" | "float" | "boolean" | "binary" | "record" | "version" | "`evr_string`"

operation ::= comparison_op | string_op | set_op | pattern_op

comparison_op ::= "equals" | "`not equal`" | "`greater than`" | "`less than`" | 
                 "`greater than or equal`" | "`less than or equal`"

string_op ::= "`case insensitive equals`" | "`case insensitive not equal`" | 
             "contains" | "`starts with`" | "`ends with`"

set_op ::= "`subset_of`" | "`superset_of`"

pattern_op ::= "`pattern match`" | "matches"
```

## Tokens

```ebnf
multiline_content ::= triple_backtick_string | raw_multiline_string
triple_backtick_string ::= '```' [^`]* '```'
raw_multiline_string ::= 'r```' [^`]* '```'

backtick_string ::= '`' backtick_content* '`'
raw_string ::= 'r`' [^`]* '`'

backtick_content ::= regular_char | newline_escape | tab_escape
regular_char ::= [^`\\]
newline_escape ::= '\n'
tab_escape ::= '\t'

identifier ::= [a-zA-Z_][a-zA-Z0-9_]*
boolean_value ::= "true" | "false" 
integer_value ::= [0-9]+
float_value ::= [0-9]+ "." [0-9]+
quoted_string ::= '"' [^"]* '"'
```

## State Placement, References, and Execution Order

### Definition-Level States
**Location**: After `DEF`, before any `SET` or `CRI` blocks  
**Scope**: Global to entire definition  
**Usage**: Shared states referenced by multiple CTN blocks, SET filters, or cross-criterion validation

### CTN-Level State References
**Location**: Within `CTN` blocks, after `TEST`, before local `STATE` or `OBJECT`  
**Syntax**: `STATE_REF state_identifier`  
**Usage**: Reference promoted definition-level states from CTN blocks

### CTN-Level States  
**Location**: Within `CTN` blocks, after `TEST` and `STATE_REF` lines, before `OBJECT`  
**Scope**: Local to single CTN block  
**Usage**: Test validation for states that are NOT shared, single-use object filters

### Complete Execution Order
```
DEF
  [Variable Declarations]    # VAR blocks
  [Runtime Operations]       # RUN blocks  
  [Definition-Level States]  # STATE blocks (promoted/shared)
  [SET Operations]           # SET blocks
  [Criteria]                 # CRI blocks
    [CTN Blocks]
      [TEST]                 # Test specification
      [State References]     # STATE_REF lines (for promoted states)
      [Local States]         # STATE blocks (CTN-specific, non-shared)
      [OBJECT]               # Object specification
DEF_END
```

## Key Features Summary

### **1. Enhanced State Management**
- Definition-level states for truly shared logic
- STATE_REF syntax for referencing promoted states from CTN blocks
- CTN-level states for non-shared, test-specific validation
- Automatic state promotion based on usage patterns

### **2. Flattened RUN Operations**
- All RUN blocks use flat parameter structure (no nesting)
- Temporary variables (`temp_X`) support operation chaining
- EXTRACT operation handles direct object component references

### **3. Enhanced Variable Support**
- Permanent variables: `var_XXXXX` format
- Temporary variables: `temp_XXXXX` format
- Full variable reference resolution in object fields

### **4. Complex Object Elements**
- Module specifications (module_name, verb, noun)
- Parameters and select elements with nested fields
- Behavior specifications with attribute-value pairs

### **5. Enhanced Filter Support**
- Multiple state references in single FILTER block
- Both object-level and SET-level filters
- Include/exclude filter actions

### **6. Record Datatype Support**
- Nested field specifications in states
- Field path notation with dot syntax
- Null value checking for record fields

## Complete Example with STATE_REF

```ics
DEF
VAR var_42400 int 0
VAR base_path string `/etc`

RUN temp_0 CONCAT
    VAR base_path
    literal `/firefox/policies`
RUN_END

RUN var_path_list SPLIT
    delimiter `/`
    VAR temp_0
RUN_END

STATE ste_shared_validation
    type string equals `reg_dword`
    value int equals VAR var_42400
STATE_END

STATE ste_filter_state
    security_principle string equals `Administrators`
STATE_END

SET obj_filtered union
    OBJECT
        security_principle `.*`
    OBJECT_END
    FILTER include
        state_ref ste_filter_state
    FILTER_END
SET_END

CRI AND
CTN registry
    TEST `at least one exists` all OR
    STATE_REF ste_shared_validation
    OBJECT obj_registry
        hive `HKEY_LOCAL_MACHINE`
        key `Software\Policies\Microsoft\Windows`
        name `TestValue1`
    OBJECT_END
CTN_END

CTN registry
    TEST `any exist` all
    STATE_REF ste_shared_validation
    OBJECT
        hive `HKEY_LOCAL_MACHINE`
        key `Software\Policies\Microsoft\Windows`
        name `TestValue2`
    OBJECT_END
CTN_END

CTN cmdlet
    TEST `any exist` all
    STATE ste_local_record_check
        result record equals
            field `issuer` string `pattern match` `^CN=DoD Root CA`
            field `thumbprint` string equals `ABC123`
    STATE_END
    OBJECT
        module_name `Microsoft.PowerShell.Management`
        verb `Get`
        noun `ChildItem`
        parameters record
            path `Cert:\LocalMachine\Root`
        select record
            property `issuer,thumbprint`
    OBJECT_END
CTN_END

CTN file
    TEST `any exist` all
    OBJECT
        path VAR temp_0
        filename `*.json`
        # Empty object - no additional fields required
    OBJECT_END
CTN_END
CRI_END
DEF_END
```

# Core Architectural Principles

## State Reference Model

### **Two-Level State System**
- **Definition-level states**: Global scope, referenced via `STATE_REF`
- **CTN-level states**: Local scope, referenced via `state_ref` in filters

### **Reference Resolution**
- All state references must resolve to existing state definitions
- `STATE_REF` lines reference definition-level states only
- Object filter `state_ref` can reference local CTN states
- SET filter `state_ref` must reference definition-level states

### **Scope Rules**
```ics
STATE ste_global            # Definition scope - accessible everywhere
CTN registry
    STATE_REF ste_global    # Cross-reference to definition state
    STATE ste_local         # CTN scope - local access only
        value int equals 1
    STATE_END
    OBJECT
        FILTER include
            state_ref ste_local    # Local reference within CTN
        FILTER_END
    OBJECT_END
CTN_END
```
## Key Design Constraints

### **Architectural Boundaries**

#### **Parser Domain (This EBNF)**
- Grammar compliance and structural validation
- Symbol resolution and type consistency
- Universal data flow patterns
- Platform-agnostic field syntax validation

#### **Compliance Scanner Domain** 
- Platform-specific field semantics (registry vs file appropriateness)
- Business logic and security policy validation
- Runtime system accessibility verification
- Domain-specific content validation

## Implementation Notes

### **Multi-Pass Architecture Required**
This grammar is designed for multi-pass compilation:
1. **Lexical + Syntax**: Complete AST construction
2. **Symbol Discovery**: Variable and state declaration collection  
3. **Reference Resolution**: Cross-reference validation within scopes
4. **Semantic Analysis**: Type checking and dependency validation

### **Forward References Supported**
The architecture permits forward references - symbols can be used before declaration since all symbols are discovered before resolution begins.

### **State Reference Scoping**
- Definition-level states: Global accessibility via `STATE_REF`
- CTN-level states: Local accessibility within CTN context
- SET filters: Must reference definition-level states only

For complete implementation guidance, validation rules, and development strategies, see the separate ICS Parser Implementation Guide.