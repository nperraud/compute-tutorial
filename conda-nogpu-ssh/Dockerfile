FROM conda-nogpu

# Install SSH server -- start here
RUN apt-get update && apt-get install -y \
    openssh-server \
    && update-locale \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/#*PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed -i 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' /etc/pam.d/sshd

ENV NOTVISIBLE="in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
# Install SSH server -- finish here


# Install VS-code -- start here
# Install necessary dependencies
RUN apt-get update && \
    apt-get install -y curl && \
    curl -fsSL https://code-server.dev/install.sh | sh

# Install Python and Jupyter extensions
RUN code-server --install-extension ms-python.python && \
    code-server --install-extension ms-toolsai.jupyter

# Copy the extensions folder
# RUN ln -s /root/.vscode-server /root/.local/share/code-server
RUN cp -r /root/.local/share/code-server /root/.vscode-server

# Install VS-code -- finish here