#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <ctype.h>
#include <math.h>
typedef struct
{
    float items;
    struct node *next;
}node;
typedef struct
{
    node *head;
} stack;

node* newnode(float items)
{
    node *nn=malloc(sizeof(node));
    nn->items=items;
    nn->next=NULL;
    return nn;
}
stack *initialize()
{
    stack *s=malloc(sizeof(stack));
    s->head=NULL;
    return s;
}
int isEmpty(stack *s)
{
    if(s->head==NULL)
        return 1;
    else return 0;
}

float peek(stack *f)
{
        return f->head->items;
}
float pop(stack *z)
{
    node *temp=z->head;
    float items=temp->items;
    z->head=temp->next;
    free(temp);
    return items;
}
void push(stack *y, float val)
{
   node* temp=newnode(val);
   temp->next=y->head;
   y->head=temp;
}
int isNegative (char *x)
{
    if(x[0] == '-' && isdigit(x[1]))
        return 1;
    else
        return 0;
}
int isfloat(char *str)
{   int i;
    for(i=0;i<strlen(str);i++)
    {
        if(str[i]=='.')
            return 1;
        else return 0;
    }
}
int floatnegative(char *str)
{
    int i;
    for(i=0;i<strlen(str);i++)
    {
        if(str[i]=='.' && str[0]=='-')
            return 1;
        else return 0;
    }
}
float evaluate(char* str)
{
    stack *x=initialize();
    char *tok, p[100];
    strcpy(p,str);
    tok=strtok(p," ");
    float d;
    while(tok!=NULL)
    {
        if(isdigit(*tok) || isNegative(tok) || isfloat(tok) || floatnegative(tok))
        {
            push(x,atof(tok));

        }
        else
        {
            float y2=pop(x), y1=pop(x);
            switch(*tok)
            {
                case'+': d=y1+y2; break;
                case'-': d=y1-y2; break;
                case'*': d=y1*y2; break;
                case'/': d=y1/y2; break;
                case'^': d=pow(y1,y2); break;
            }
        push(x,d);
        }
        tok=strtok(NULL," ");
    }
    return (peek(x));
}
int priority(char ch)
{
    
	if(ch == '^')
	{
		return(3);
	}
	else if(ch == '*' || ch == '/')
	{
		return(2);
	}
	else if(ch == '+' || ch == '-')
	{
		return(1);
	}
	else
	{
		return(0);
	}
}
char *preEvaluate(char *postfix)
{
    int i=0,k=0;
    char *pF=(char*)malloc(50*sizeof(char));
    char ch;
    while( (ch=postfix[i++]) != '\0')
    {
        if((ch==' ') && (postfix[i])==' ')
        {
            pF[k++]=ch;
            i++;
        }
        else if(!isalnum(ch) && !isalnum(postfix[i]))
        {
          pF[k++]=' ';
          pF[k++]=ch;
          pF[k++]=' ';
          ch=postfix[i++];
          pF[k++]=ch;
          
        }
        else
        {
          pF[k++]=ch;
        }
        
    }
    pF[k++]='\0';
    
    return pF;
}
char * removeBlanks(const char * str)
{
    int i, j;
    char * newString;
    newString = (char *)malloc(50);
    i = 0;
    j = 0;
    while(str[i] != '\0')
    {
        if(str[i] == ' ')
        {
            newString[j] = ' ';
            j++;
            while(str[i] == ' ')
                i++;
        }
        newString[j] = str[i];
        i++;
        j++;
    }
    newString[j] = '\0';
    return newString;
}
char *infixToPostfix(char *infix)
{
    char *postfix=(char*)malloc(50*sizeof(char));
    stack *s=initialize();
    char ch,elem;
    int i=0,k=0;
    push(s,'#');
    while( (ch=infix[i++]) != '\0')
    {
        while( (infix[i]) == ' '){i++;}
        if( ch == '(') push(s,ch);
        else
            if(isalnum(ch) && isalnum(infix[i]))
            {
                if(postfix[k-1]=='-'){postfix[k-1]=' ';postfix[k++]='-';}
                //else{ postfix[k++]=' ';}
                postfix[k++]=ch;
                ch=infix[i++];
                postfix[k++]=ch;
                postfix[k++]=' ';
            }
            else if(isalnum(ch) && (infix[i]=='.'))
            {
                if(postfix[k-1]=='-'){postfix[k-1]=' ';postfix[k++]='-';}
                else{ postfix[k++]=' ';}
                postfix[k++]=ch;
                
                ch=infix[i++];
                postfix[k++]=ch;
                
                while(isalnum(infix[i]))
                {
                    ch=infix[i++];
                    postfix[k++]=ch;
                }
                postfix[k++]=' ';
            }
            else if(isalnum(ch))
            {
              postfix[k++]=ch;//(isalnum)checks if number or letter otherwise return 0
              postfix[k++]=' ';
              
            }
            else
            {
                if( ch == ')')
                {
                    while( peek(s) != '(')
                        postfix[k++]=pop(s);
                        //postfix[k++]=' ';
                    elem=pop(s); // remove
                }
                else if( ch == '-' && (!isalnum(infix[i-2])) )
                {
                    postfix[k++]=ch;
                }
                else
                {       // Operator 
                    while( priority(peek(s)) >= priority(ch) )
                        postfix[k++]=pop(s);
                        postfix[k++]=' ';
                    push(s,ch);
                }
            }
    }
    while( peek(s) != '#')    //Pop from stack till empty
        postfix[k++]=pop(s);
    //while
    //   
    postfix[k]='\0';          // Make postfix as valid string
    printf("\nPostfix Expression =  %s\n",removeBlanks(preEvaluate(postfix)));
    
    return removeBlanks(preEvaluate(postfix));
}

int main()
{
    char *exp1 = "1+2*4+3";
    char *exp2 = "(1+2)*4+3";
    //char *exp3 = "10+3*5/(16–4)";doesn't work although every condition works separately
    char *exp3 = "20*45-10*-1+0.5";
    char *exp4 = "2+3*4";
    char *exp5 = "2+(-2.5+3.14)*(-5.4+8.1)^(-0.5)";
    printf ("postfix evaluation: %f\n", evaluate(infixToPostfix(exp1)));
    printf ("postfix evaluation: %f\n", evaluate(infixToPostfix(exp2)));
    printf ("postfix evaluation: %f\n", evaluate(infixToPostfix(exp3)));
    printf ("postfix evaluation: %f\n", evaluate(infixToPostfix(exp4)));
    printf ("postfix evaluation: %f\n", evaluate(infixToPostfix(exp5)));
    return 0;
}
