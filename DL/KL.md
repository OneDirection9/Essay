# KL

[Reverse KL vs forward KL](https://www.tuananhle.co.uk/notes/reverse-forward-kl.html)

> About the two directions of KL, I have one intuitive way to remember which direction does what.
> For KL\[p || q\], if you expand out, you will have log(p(x)/q(x)) inside the integral.
> When is this quantity high ? It is when p(x) is high and q(x) is low.
> So to prevent it from being high (we want to minimize it), you must spread the mass of q(x) everywhere that p(x) has some mass.
> That is why it is mean seeking.
>
> For KL\[q || p\], expanding out gives you log(q(x)/p(x)).
> This one is high when q(x) is high and p(x) is low.
> So to minimize, you want to allocate mass of q in the region where p is high
> (imagine that q(x) and p(x) are roughly equal in the local region and log(q(x)/p(x)) is roughly log(1)=0.
> Now what if q(x) is low when p(x) is high ? This is okay because the quantity is still low.
> That is to say that q may completely omit some mode. That is why it is mode seeking.
