
## 状态机原理

在根据当前状态(cur_state) 下，发生事件(event)后，转移到下一个状态号(nxt_state)，决定执行的动作(action)。

发生事件event --> cur_state --> 执行动作action
                           --> 设置下一个状态next_state

## 结构体

这里我们首先定义一个结构体如下：
```c
typedef struct {
  State curState;//当前状态
  EventID eventId;//事件ID
  State nextState;//下个状态
  Action action;//具体表现
}StateTransform;
```

## 状态枚举

我们假设有3种状态，这里可以随意增加，状态枚举如下：
```c
typedef enum {
  state_1=1,
  state_2,
  state_3
}State;
```

## 事件

我们假设有5个事件，也可以随意增加，事件ID枚举如下：
```c
typedef enum{
  event_1=1,
  event_2,
  event_3,
  event_4,
  event_5
}EventID;
```

## 状态机

将其封装起来在StateMachine中：
```c
typedef struct{
  State state;
  int transNum;
  StateTransform* transform;
}StateMachine;
```

## 具体流程

当前状态-有事件触发-跳到下个状态-具体表现，重构代码
```c
StateTransform* findTranss(StateMachine* pSM,  const EventID evt)
{
  int i;
  for (i = 0; i < pSM->transNum; i++) {
    if ((pSM->transform[i].curState == pSM->state) && (pSM->transform[i].eventId == evt)) {
      return &pSM->transform[i];
    }
  }
  return NULL;
}
状态机实现如下：

void runStateMachine(StateMachine* pSM, EventID evt) {
  StateTransform* pTrans;
  pTrans = findTranss(pSM, evt);
  if (pTrans == NULL)
  {
    xil_printf( "CurState= %s Do not process enent: %s\r\n", pSM->state,evt);
    return;
  }
  pSM->state = pTrans->nextState;
  Action act = pTrans->action;
  if (act == NULL) {
    xil_printf( "change state to %s. No action\r\n",pSM->state);
    return;
  }
  act(&evt);
}
最后我模拟一些随机事件，我们只需要弄清楚事件ID，状态切换，具体表现就可以了，在代码中就是填写stateTran[] 这个表，一旦有增减事件，状态等等，也不需要再去使用switch/case，特费脑，其代码如下：

int run()
{
  StateMachine stateMachine;
  stateMachine.state = state_1;
  stateMachine.transNum = 7;
  StateTransform stateTran[] = {
    {state_1,event_3,state_2,f121},
    {state_1,event_4,state_2,NULL},
    {state_2,event_1,state_3,f231},
    {state_2,event_4,state_2,f221},
    {state_3,event_2,state_1,f311},
    {state_3,event_3,state_2,f321},
    {state_3,event_5,state_3,f331}
  };
  stateMachine.transform = stateTran;

  EventID inputEvent[15] = 
  { event_1, event_2, event_3, event_4, event_5,
    event_1, event_2, event_3, event_4, event_5,
    event_1, event_2, event_3, event_4, event_5 };

  int i;
  for (i = 0; i < 15; i++) {
    runStateMachine(&stateMachine, inputEvent[i]);
  }
  return 0;
}
```

