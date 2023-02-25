# Drools动态加载规则

## 方式一：使用KieHelper封装类

```java
public Order loadRuleTest(Order order){
    String drl = "package rules;\n" +
            "\n" +
            "import com.honma.droolsspringboot.entity.Order;\n" +
            "\n" +
            "rule \"test_1\"\n" +
            "when\n" +
            "    $order:Order(amount>5000)\n" +
            "then\n" +
            "    $order.setScore(9999);\n" +
            "    System.out.println(\"触发了规则：test10\");\n" +
            "end\n";
    kieHelper.addContent(drl , ResourceType.DRL);
    Results results = kieHelper.verify();
    if (results.hasMessages(Message.Level.WARNING, Message.Level.ERROR)) {
        List<Message> messages = results.getMessages(Message.Level.ERROR);
        for (Message message : messages) {
            throw new RuntimeException("解析drools字符串出错:" + message.getText());
        }
    }
    KieBase base = kieHelper.build();
    KieSession session = base.newKieSession();
    session.insert(order);
    session.fireAllRules();
    session.destroy();
    return order;
}
```

测试结果：第二次调用时异常 该rule已存在

## 方式二：不使用KieHelper封装类

```Java
public class RuleUtils {
    
    /**
     * 由从数据库里面读出的drl构建KieSession
     * @param drl
     * @return
     * @throws UnsupportedEncodingException
     */
    public static KieSession getKieSessionFromDrl(String drl) throws UnsupportedEncodingException {
        KieSession tempKieSession = null;
        KnowledgeBuilder kb = KnowledgeBuilderFactory.newKnowledgeBuilder();
        kb.add(ResourceFactory.newByteArrayResource(drl.getBytes("utf-8")), ResourceType.DRL);
        if (kb.hasErrors()) {
            String errorMessage = kb.getErrors().toString();
            System.out.println("规则语法异常---\n"+errorMessage);
            return null;
        }
        InternalKnowledgeBase kBase = KnowledgeBaseFactory.newKnowledgeBase();
        kBase.addPackages(kb.getKnowledgePackages());
        tempKieSession = kBase.newKieSession();
        return tempKieSession;
    }
}

public Order dynamicRules(Order order, String drl){
        try {
            KieSession kieSession = RuleUtils.getKieSessionFromDrl(drl);
            kieSession.insert(order);
            kieSession.fireAllRules();
            kieSession.destroy();
        } catch (UnsupportedEncodingException e) {
            throw new RuntimeException(e);
        }
        return order;
    }
```

测试结果：可重复调用，缺点：每次调用都要生成一个新的KieSession，即使drl没有改变



## 方式三：使用KieFileSystem虚拟文件



1. 使用 KieFileSystem 创建一个基于内存的虚拟文件系统，kfs 中的文件路径规范参考 ClasspathContainer 方式
2. KieBuilder 使用 kfs 中的 kmodule.xml 以及规则文件创建 KieModule（KieBuilder 内部再将 KieModule 保存在了 KieRepository）
3. 通过 releaseId 创建 KieContainer，如果 kfs 中未指定 pom，则需要将`ks.getRepository().getDefaultReleaseId()` 作为参数传入

## 方式四：KieScanner

通过动态加载jar的方式来实现资源加载和动态更新