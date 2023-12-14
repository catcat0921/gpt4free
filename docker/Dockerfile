FROM selenium/node-chrome

ENV SE_SCREEN_WIDTH 1850
ENV PYTHONUNBUFFERED 1
ENV G4F_DIR /app
ENV G4F_LOGIN_URL http://localhost:7900/?autoconnect=1&resize=scale&password=secret
ARG G4F_VERSION
ENV G4F_VERSION ${G4F_VERSION}
ARG G4F_USER
ENV G4F_USER ${G4F_USER:-g4f}
ARG G4F_USER_ID
ENV G4F_USER_ID ${G4F_USER_ID:-1000}
ARG G4F_NO_GUI
ENV G4F_NO_GUI ${G4F_NO_GUI}
ENV HOME /home/$G4F_USER

USER root

#  If docker compose, install git
RUN if [ "$G4F_VERSION" = "" ] ; then \
  apt-get -qqy update && \
  apt-get -qqy install git \
  ; fi

# Python packages
RUN apt-get -qqy update \
  && apt-get -qqy install \
    python3 \
    python-is-python3 \
    pip

# Cleanup
RUN rm -rf /var/lib/apt/lists/* /var/cache/apt/* \
  && apt-get -qyy autoremove \
  && apt-get -qyy clean

# Update entrypoint
COPY docker/supervisor.conf /etc/supervisor/conf.d/selenium.conf
COPY docker/supervisor-gui.conf /etc/supervisor/conf.d/gui.conf

# If no gui
RUN if [ "$G4F_NO_GUI" ] ; then \
  rm /etc/supervisor/conf.d/gui.conf \
  ; fi

# Change background image
COPY docker/background.png /usr/share/images/fluxbox/ubuntu-light.png

# Switch user
RUN groupadd -g $G4F_USER_ID $G4F_USER
RUN useradd -rm -G sudo -u $G4F_USER_ID -g $G4F_USER_ID $G4F_USER
USER $G4F_USER_ID

# Set the working directory in the container.
WORKDIR $G4F_DIR

# Copy the project's requirements file into the container.
COPY requirements.txt $G4F_DIR

# Upgrade pip for the latest features and install the project's Python dependencies.
RUN pip install --upgrade pip && pip install -r requirements.txt

# Copy the entire package into the container.
ADD --chown=$G4F_USER g4f $G4F_DIR/

# Expose ports
EXPOSE 8080 1337