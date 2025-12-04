```java

@Slf4j
@RestControllerAdvice
public class GlobalExceptionHandler {

    /**
     * ✅ MySQL 字段过长捕获
     */
    @ExceptionHandler(DataIntegrityViolationException.class)
    public BaseResponse<?> handleDataIntegrity(DataIntegrityViolationException e) {
        log.error("数据库完整性异常：{}", e.getMessage(), e);
        String msg = getCauseMessage(e);

        if (msg.contains("Data too long for column")) {
            return failWithColumn(msg, "字段内容过长，请检查输入");
        }
        if (msg.contains("cannot be null")) {
            return failWithColumn(msg, "字段不能为空");
        }
        if (msg.contains("Incorrect")) { // date、int、decimal等格式错误
            return fail(ResponseCode.FAIL.getCode(), "字段格式有误，请检查输入");
        }
        if (msg.contains("foreign key constraint")) {
            return fail(ResponseCode.FAIL.getCode(), "存在关联数据，请先解除关联");
        }
        if (msg.contains("Duplicate entry")) {
            return fail(ResponseCode.FAIL.getCode(), "存在重复数据，请检查唯一约束");
        }

        return unknownDBError(msg, e);
    }

    /**
     * ✅ 处理未分类 SQL 异常（兼容 MyBatis 包装）
     */
    @ExceptionHandler(UncategorizedSQLException.class)
    public BaseResponse<?> handleUncategorizedSQLException(UncategorizedSQLException e) {
        log.error("未分类 SQL 异常：{}", e.getMessage(), e);
        return dispatchSQLException(e.getSQLException(), e);
    }

    /**
     * ✅ SQL 语法错误
     */
    @ExceptionHandler(BadSqlGrammarException.class)
    public BaseResponse<?> handleBadSqlGrammar(BadSqlGrammarException e) {
        log.error("SQL 语法错误：{}", e.getMessage(), e);
        return fail(ResponseCode.FAIL.getCode(), "数据库语句执行失败，请检查 SQL");
    }

    /**
     * ✅ 参数类异常
     */
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public BaseResponse<?> handleParamValid(MethodArgumentNotValidException e) {
        String msg = e.getBindingResult().getFieldErrors().stream()
                .map(x -> x.getField() + ":" + x.getDefaultMessage())
                .collect(Collectors.joining(","));
        return fail(ResponseCode.PARAM_IS_ERROR.getCode(), msg);
    }

    @ExceptionHandler(ConstraintViolationException.class)
    public BaseResponse<?> handleConstraintValid(ConstraintViolationException e) {
        return fail(ResponseCode.PARAM_IS_ERROR.getCode(), e.getMessage());
    }

    /**
     * ✅ 业务异常
     */
    @ExceptionHandler(ServiceException.class)
    public BaseResponse<?> handleService(ServiceException e) {
        log.error("业务异常：{}", e.getMessage());
        return fail(e.getCode(), e.getMessage());
    }

    /**
     * ✅ 兜底异常（保证系统不会爆栈）
     */
    @ExceptionHandler(Exception.class)
    public BaseResponse<?> handleException(Exception e) {
        log.error("未知异常：{}", e.getMessage(), e);
        return fail(ResponseCode.FAIL.getCode(), "系统错误，请稍后再试");
    }


    /* =================== 私有封装 =================== */

    private BaseResponse<?> dispatchSQLException(SQLException ex, Exception origin) {
        String msg = getCauseMessage(ex);

        if (msg.contains("Data too long for column")) {
            return failWithColumn(msg, "字段内容过长，请检查输入");
        }
        if (msg.contains("Duplicate entry")) {
            return fail(ResponseCode.FAIL.getCode(), "存在重复数据，请检查唯一约束");
        }
        if (msg.contains("Incorrect")) {
            return fail(ResponseCode.FAIL.getCode(), "字段格式有误，请检查输入");
        }

        return unknownDBError(msg, origin);
    }

    private BaseResponse<?> failWithColumn(String msg, String tip) {
        String field = extractColumn(msg);
        String finalMsg = (field != null)
                ? "字段【" + field + "】" + tip
                : tip;
        return fail(ResponseCode.FAIL.getCode(), finalMsg);
    }

    private BaseResponse<?> unknownDBError(String msg, Exception e) {
        log.error("数据库未知错误：{}", msg, e);
        return fail(ResponseCode.FAIL.getCode(), "数据库执行失败，请检查输入或联系管理员");
    }

    private String getCauseMessage(Throwable e) {
        Throwable t = e.getCause() != null ? e.getCause() : e;
        return t.getMessage() == null ? "" : t.getMessage();
    }

    private BaseResponse<?> fail(int code, String msg) {
        return ResponseUtil.fail(code, StringUtils.defaultString(msg, "失败"));
    }

    /** 提取字段名 */
    private static final Pattern COLUMN_PATTERN =
            Pattern.compile("for column '(.*?)'|(Column '(.*?)')");

    private String extractColumn(String msg) {
        Matcher matcher = COLUMN_PATTERN.matcher(msg);
        if (matcher.find()) {
            return matcher.group(1) != null ? matcher.group(1) : matcher.group(3);
        }
        return null;
    }
}

```