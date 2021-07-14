FROM ubuntu

RUN echo '${user:-default_user}: ' ${user:-default_user}
RUN echo '${user:+default_user}: ' ${user:+default_user}
ARG user=default_user
RUN echo '${user}: ' $user
