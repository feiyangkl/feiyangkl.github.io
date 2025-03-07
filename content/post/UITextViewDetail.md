---
title: "UITextView详解"

date: "2018-10-22 10:29:32"

tags: 
    - iOS
---


之前看到很多好朋友问到UITextView限制字数，或者限制成只输入数字等，接下来见到了解下UITextView。

##TextView简单属性
```
//初始化UITextView
      UITextView *textview =  [[UITextView alloc] initWithFrame:CGRectMake(100, 100, 300, 44)];
    textview.backgroundColor=[UIColor blueColor]; //设置背景色
    textview.scrollEnabled = NO;    //设置当文字超过视图的边框时是否允许滑动，默认为“YES”
    textview.editable = YES;        //设置是否允许编辑内容，默认为“YES”
    textview.delegate = self;       //设置代理方法的实现类
    textview.font=[UIFont fontWithName:@"Arial" size:18.0]; //设置字体名字和字体大小;
    textview.returnKeyType = UIReturnKeyDefault;//设置return键的类型
    textview.keyboardType = UIKeyboardTypeDefault;//设置键盘类型一般为默认
    textview.textAlignment = NSTextAlignmentLeft; //文本显示的位置默认为居左
    textview.dataDetectorTypes = UIDataDetectorTypeAll; //显示数据类型的连接模式（如电话号码、网址、地址等）
    textview.textColor = [UIColor blackColor];// 设置显示文字颜色
    textview.text = @"UITextView详解";//设置显示的文本内容
    [self.view addSubview:textview];
```
<!-- more --> 
##TextView的代理方法
```object-c
//将要开始编辑
- (BOOL)textViewShouldBeginEditing:(UITextView *)textView;

//将要结束编辑
- (BOOL)textViewShouldEndEditing:(UITextView *)textView;

//开始编辑
- (void)textViewDidBeginEditing:(UITextView *)textView;

//结束编辑
- (void)textViewDidEndEditing:(UITextView *)textView;

//内容将要发生改变编辑
- (BOOL)textView:(UITextView *)textView shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text;

//内容发生改变编辑
- (void)textViewDidChange:(UITextView *)textView;

//焦点发生改变
- (void)textViewDidChangeSelection:(UITextView *)textView;

//控件自适应输入的文本的内容的高度，只要在textViewDidChange的代理方法中加入调整控件大小的代理即可
- (void)textViewDidChange:(UITextView *)textView{
    //计算文本的高度
    CGSize constraintSize;
    constraintSize.width = textView.frame.size.width-16;
    constraintSize.height = MAXFLOAT;
    CGSize sizeFrame =[textView.text sizeWithFont:textView.font
                                constrainedToSize:constraintSize
                                    lineBreakMode:UILineBreakModeWordWrap];
    
    //重新调整textView的高度
    textView.frame = CGRectMake(textView.frame.origin.x,textView.frame.origin.y,textView.frame.size.width,sizeFrame.height+5);
}

// 控制输入文字的长度和内容，可调用一下方法
- (BOOL)textView:(UITextView *)textView shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text
{
    if (range.location>=32)
    {
        //控制输入文本的长度
        return  NO;
    }
    if ([text isEqualToString:@"\n"]) {
        //禁止输入换行
        return NO;
    }
    else
    {
        return YES;
    }
}

```

##UITextVIew的限制数字的Demo->显示金额
```

- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string;

{  //string就是此时输入的那个字符 textField就是此时正在输入的那个输入框 返回YES就是可以改变输入框的值 NO相反
    
    if ([string isEqualToString:@"\n"])  //按会车可以改变
    {
        return YES;
    }
    
    
    NSString * toBeString = [textField.text stringByReplacingCharactersInRange:range withString:string]; //得到输入框的内容
    
    if (self.tf_signsum == textField)  //判断是否时我们想要限定的那个输入框
    {
        // 判断首位不能为零
        if (toBeString.length > 0) {
            if ([toBeString doubleValue]== 0) {
               
              //判断首位不能为零
                return NO;
            }
        }
        
        
        if (![self validateNumber:toBeString]) {
           
           //提示请输入数字
            return NO;
        }
        
        if ([toBeString length] > 12) { //如果输入框内容大于12则弹出警告
            
            textField.text = [toBeString substringToIndex:12];
           
           //最多输入12位 
            return NO;
        }
        
        
        self.lb_AmountInwords.text = [XHTConversionStringTools stringWithAmountInwords:toBeString];
        
        
    }
    if (self.tf_remark == textField)  //判断是否时我们想要限定的那个输入框
    {
        if ([toBeString length] > 30) { //如果输入框内容大于20则弹出警告
            
            textField.text = [toBeString substringToIndex:30];
            
            [UIToastView showToastViewWithContent:@"最多只能输入30个文字" andRect:KTOASTRECT andTime:1 andObject:self];
            
            return NO;
            
        }
        
    }
    
    return YES;
    
}

// 是否包含0123456789
- (BOOL)validateNumber:(NSString*)number {
    BOOL res = YES;
    NSCharacterSet* tmpSet = [NSCharacterSet characterSetWithCharactersInString:@"0123456789"];
    int i = 0;
    while (i < number.length) {
        NSString * string = [number substringWithRange:NSMakeRange(i, 1)];
        NSRange range = [string rangeOfCharacterFromSet:tmpSet];
        if (range.length == 0) {
            res = NO;
            break;
        }
        i++;
    }
    return res;
}
```

##UITextView退出键盘的方法
>1.如果你程序是有导航条的，可以在导航条上面加多一个Done的按钮，用来退出键盘，当然要先实UITextViewDelegate。
 
 ```
 
- (void)textViewDidBeginEditing:(UITextView *)textView {
    
    UIBarButtonItem *done =    [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemDone
                                                                             target:self
                                                                             action:@selector(dismissKeyBoard)];
    
    self.navigationItem.rightBarButtonItem = done;
    
    [done release];
    done = nil;
    
}

- (void)textViewDidEndEditing:(UITextView *)textView {  
    self.navigationItem.rightBarButtonItem = nil;   
}

- (void)dismissKeyBoard {   
    [self.textView resignFirstResponder];  
}
```

>2.如果textview里不用回车键，可以把回车键当做退出键盘的响应键。

```
-(BOOL)textView:(UITextView *)textView shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text
{
    if ([text isEqualToString:@"\n"]) {
        [textView resignFirstResponder];
        return NO;
    }
    return YES;
}
```